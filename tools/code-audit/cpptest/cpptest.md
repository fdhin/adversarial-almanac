---
name: "cpptest"
categories:
  - blackarch-code-audit
version: "2.0.0"
homepage: "https://github.com/cpptest/cpptest/releases"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2019-05-09"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "cpptest/cpptest"
github-stars: 78
github-pushed: "2026-09-27"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# cpptest

> CppTest: a portable C++ unit-testing framework library with pluggable output handlers.

## What it's for

CppTest is a library, not a command-line tool: tests are C++ classes deriving from `Test::Suite` with cases registered through the `TEST_ADD` macro, linked against `-lcpptest` into your own test binary. It ships output handlers for plain text (terse and verbose), compiler-style diagnostics (so IDEs jump straight to the failing assertion), and HTML reports, and new formats are added by subclassing `Test::Output`. Reach for it when a small, dependency-light test harness is wanted in an autotools or plain-make C++ project. The upstream repository is archived; version 2.0.0 (2008) is the final and current release.

## Typical use cases

### Write, build, and run a first test suite

Create a minimal suite, compile it against the library, and run it.

```
cat > tests.cpp <<'EOF'
#include <cpptest.h>

class MathTests : public Test::Suite {
public:
    MathTests() { TEST_ADD(MathTests::addition); }
private:
    void addition() { TEST_ASSERT(1 + 1 == 2); }
};

int main() {
    MathTests suite;
    Test::TextOutput output(Test::TextOutput::Verbose);
    return suite.run(output) ? 0 : 1;
}
EOF
g++ tests.cpp -lcpptest -o tests && ./tests
```

Outcome: a verbose text report listing each test case and its assertions; `run()` returns true only when every assertion holds, so the binary's exit status works directly under `make check` or CI.

### Switch the report format

```
./tests --compiler
./tests --html > report.html
```

Outcome: `--compiler` prints failures in compiler-diagnostic form (file:line: message) so editors and IDEs can jump to the failing assertion; `--html` writes an HTML report to stdout, redirected here into report.html. These are the modes the bundled sample (test/mytest.cpp) demonstrates — `--text-terse` and `--text-verbose` complete the set, with verbose as the default.

### Resolve build flags via pkg-config

```
g++ $(pkg-config --cflags --libs libcpptest) tests.cpp -o tests
```

Outcome: the installed `libcpptest` pkg-config module supplies the include and library paths, so the build line works regardless of where the library was installed.

*No corpus evidence — derived from upstream documentation.*
