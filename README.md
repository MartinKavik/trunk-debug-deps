trunk-debug-deps

```bash
/d/repos/trunk-debug-deps/app (master)
$ trunk serve
Error during execution of `cargo metadata`: error: package collision in the lockfile: packages dummy_dep v0.1.0 (D:\repos\trunk-debug-deps\app\../dummy_dep) and dummy_dep v0.1.0 (D:\repos\trunk-debug-deps\dummy_dep) are different, but only one can be written to lockfile unambiguously
 server running at http://127.0.0.1:8080/
   trunk | error
```
