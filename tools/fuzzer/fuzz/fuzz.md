---
name: "fuzz"
gentoo-category: "app-fuzz"
version: "0.4"
homepage: "http://taviso.decsystem.org/software.html#toc2"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fuzz

> Tavis Ormandy's bash-driven file fuzzer: corrupts a seed file while your quoted command processes it.

## What it's for

fuzz is a hybrid bash script that performs classic file-format fuzzing. You give it a quoted command line containing the `__FILE__` placeholder plus a valid seed file; it repeatedly corrupts the file, re-runs the command with `__FILE__` substituted, and classifies each run from the program's exit code. Performance-critical primitives (byte read/write/seek, alarms, timing) are compiled into a small C DSO, `builtins.so`, loaded as bash builtins via `enable -f`. The corruption follows named "decay strategies" (Mexican Wave, Dword Wrap, Swap, Populate, Shrink, Increment, Rotate, ...) and then falls back to endless random corruption until interrupted. Inputs that crash the program are saved gzipped with a unique number. It is a single-program file fuzzer — not a network or protocol fuzzer, and despite its old packaging description it contains no Python.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a program against a seed file

Minimal run: quote the command, reference the input with `__FILE__`, and pass a small valid seed file.

```
fuzz "cat __FILE__" input.txt
```

Outcome: fuzz corrupts `input.txt` in place according to its decay strategies and re-runs `cat` as fast as possible, printing a live progress line with the current strategy, executions per second, success/error percentages, crash count, and timeout count. Stop it with Ctrl-C once satisfied.

### Bound run time per execution and keep timeout inputs

Set a timeout and retain inputs that hang the program rather than only those that crash it.

```
fuzz -T 10 -k "grep -q foobar __FILE__" input.txt
```

Outcome: each execution is killed after 10 seconds (default is 60); files causing timeouts are kept instead of discarded, alongside any crashing inputs, so hangs can be triaged too.

### Save crashing inputs to a dedicated directory

Direct where fuzz stores the gzip-compressed crashers it finds.

```
fuzz -k -T10 -d /tmp/crashes "convert png:__FILE__ png:/dev/null" input.png
```

Outcome: any input that terminates the command abnormally is copied, gzipped, and stored under `/tmp/crashes` with a unique number — the seed corpus for reproducing and minimizing the crash. Redirection and pipelines work inside the quoted command string.

### Remap a program's exit codes

Teach fuzz how a program that violates UNIX exit-code conventions reports failure.

```
fuzz -a3 "bzip2 -dc < __FILE__" input.bz2
```

Outcome: exit code 3 is reinterpreted as abnormal termination, so bzip2's signal-intercepting behavior is classified as a crash instead of a handled error. The same form works for `-e` (error), `-s` (success), `-t` (timeout), and `-f` (failure to execute), repeatable as needed.

### Split the workload across machines or cores

Assign each instance a slice of the search arena.

```
fuzz -m 1:8 -k -T10 "strings -a __FILE__" a.out
```

Outcome: this instance fuzzes partition 1 of 8; running `fuzz -m 2:8 ...`, `fuzz -m 3:8 ...` and so on with the same seed file on other machines (or other cores) divides the corruption space instead of duplicating it.
