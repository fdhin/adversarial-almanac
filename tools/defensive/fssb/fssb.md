---
name: "fssb"
categories:
  - blackarch-defensive
version: "73.51d2ac2"
homepage: "https://github.com/adtac/fssb"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-24"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "adtac/fssb"
github-stars: 407
github-pushed: "2020-12-19"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fssb

> Run any program inside a filesystem sandbox: its writes land in a throwaway /tmp copy while it keeps reading the real system normally.

## What it's for

FSSB (Filesystem Sandbox for Linux) lets you execute a program and be certain it changes none of your files. It uses ptrace to stop the child just before and after each syscall and rewrites file paths so that creates, writes, renames and deletes are redirected into a sandbox directory under /tmp — the child sees its changes succeed and cannot detect the substitution, while your real filesystem stays untouched. Use it to dry-run untrusted binaries, installers or scripts and to observe exactly which paths a program tries to modify. It is alpha-quality and x86_64-only: good for experiments and analysis, not a hard security boundary.

## Typical use cases

### Dry-run an untrusted program

Execute a binary without letting it modify anything real.

```
./fssb -- ./program arg1 arg2
```

Outcome: the program runs to completion and fssb reports `fssb: child exited with <code>`; every file it created or modified actually lives in a new `/tmp/fssb-N` sandbox directory, and the original paths are unchanged.

### List which files the program touched

Get a report of every path the sandboxed program wrote.

```
./fssb -m -- ./program
```

Outcome: after the child exits, prints a proxy map of the form `+ <md5> = <path>` for each sandboxed file, showing exactly what the program tried to create or modify.

### Keep the fssb report out of the program's output

Separate sandbox logging from the child's own output.

```
./fssb -o fssb.log -- ./program
```

Outcome: fssb writes its log output (including the proxy map) to fssb.log instead of stderr, leaving the terminal showing only the wrapped program's output.

### Clean up after inspection

Run, report, then remove the sandbox.

```
./fssb -r -m -- ./program
```

Outcome: same dry run and file map as above, but all temporary sandbox files are deleted when the program exits, leaving no trace behind.

*No corpus evidence — derived from upstream documentation.*
