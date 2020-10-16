# trunk-debug-deps

Repo with minimal example to debug Trunk issue [Dependency conflicts with Trunk 0.7.0+](https://github.com/thedodd/trunk/issues/82). (And related Cargo issue, see below.)

## How to reproduce:
In `git-bash` on Windows:

```bash
cd app
cargo metadata --manifest-path='\\?\D:\repos\trunk-debug-deps\app\Cargo.toml'
```
(Note: Modify path as you need; only format is important (`\\?..`))

or indirectly through Trunk 0.7.2:

```bash
/d/repos/trunk-debug-deps/app (master)
$ trunk serve
Error during execution of `cargo metadata`: error: package collision in the lockfile: packages dummy_dep v0.1.0 (D:\repos\trunk-debug-deps\app\../dummy_dep) and dummy_dep v0.1.0 (D:\repos\trunk-debug-deps\dummy_dep) are different, but only one can be written to lockfile unambiguously
 server running at http://127.0.0.1:8080/
   trunk | error
```

## Cargo issue

Related Cargo issue: ["Package collision in lockfile" using UNC/root local device/etc paths](https://github.com/rust-lang/cargo/issues/6198)
- Note: We often can't use workaround with path separator `\\` (instead of `/`) because then some libraries, that use only hardcoded `/` separators, fail (e.g. [proc-macro-hack](https://github.com/dtolnay/proc-macro-hack/blob/3da1be8f9519c725b1f425a954b06a1a0a8b25ac/nested/src/lib.rs#L43)).  
- Tested with `rustc 1.47.0 (18bf6b4f0 2020-10-07)` on `Windows 10`.

## Applied workaround in Trunk:

  1. `fs::write(format!("{}/index.html", self.cfg.dist.display()), output_html.as_bytes())` changed to `fs::write(self.cfg.dist.join("index.html"), output_html.as_bytes())`.
  
  2. `cmd.manifest_path(manifest);` changed to ` cmd.manifest_path(dunce::simplified(manifest));`.
     - [dunce](https://crates.io/crates/dunce)
     - `manifest` is a `Path` returned from `fs::canonicalize()`. Alternatively we can use `dunce::canonicalize` instead all `fs::canonicalize` calls and then remove `dunce::simplified` call.
