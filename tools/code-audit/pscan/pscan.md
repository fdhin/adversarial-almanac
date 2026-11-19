---
name: "pscan"
categories:
  - blackarch-code-audit
version: "1.3"
homepage: "http://deployingradius.com/pscan/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pscan

> Minimal C source scanner that flags non-constant format strings passed to format-style library calls — the classic format-string bug class.

*No corpus evidence — derived from upstream documentation.*

## What it's for

pscan applies a single heuristic to C source: if the last parameter of a known problem function is the format string, and that format string is not a static string, complain. That one rule catches the `sprintf`/`fprintf`/`snprintf` family of bugs that lead to buffer overflows and format-string attacks. It is a deliberately tiny tool (a ~100 line C program by Alan DeKok, version 1.3, 2007, no longer developed): it knows only the problem functions built in plus any you define yourself, and its findings are leads for manual review, not proof of a vulnerability. There is no directory recursion — pass files or globs on the command line.

## Typical use cases

### Scan C files for format-string abuse

Check a file set in one pass.

```
pscan src/*.c
```

Outcome: prints one line per finding giving the source file, line number and problematic function name; exits with status 1 if any problem was found (0 when clean), so it drops directly into scripted checks. `pscan ./pscan.c`-style self-scans are the upstream sanity check: a clean tree exits 0.

### Include non-constant format-argument warnings

Widen the net beyond the core rule.

```
pscan -w daemon/*.c
```

Outcome: in addition to the core rule, warns wherever a non-constant string is used as a format parameter (behavior added in version 1.2) — noisier, but catches constructions the core rule skips. Add `-v`/`-vv` for verbose output explaining what it is doing.

### Teach it project-specific functions

Cover wrappers around the standard functions.

```
pscan -p wu-ftpd.pscan src/*.c
```

Outcome: loads extra problem-function definitions from the given file (the shipped `wu-ftpd.pscan` documents the format), so a project's own `log_write(const char *fmt, ...)`-style wrappers around `printf`/`syslog` get the same last-argument format-string check as the built-ins. The companion `find_formats.sh` script helps discover candidate prototypes in existing headers.
