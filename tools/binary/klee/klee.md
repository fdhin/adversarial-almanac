---
name: "klee"
categories:
  - blackarch-binary
version: "2.1"
homepage: "https://github.com/klee/klee"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2020-05-06"
build-system: cmake
license: custom:UIUC
meta-checked: "2026-11-09"
github-repo: "klee/klee"
github-stars: 2998
github-pushed: "2026-11-04"
github-archived: false
github-issues: 81
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 126
gh-activity-checked: "2026-11-09"
---

# klee

> Symbolic execution of LLVM bitcode: explore program paths and emit a concrete test input for each.

## What it's for

KLEE is a symbolic virtual machine built on the LLVM compiler
infrastructure. You compile C/C++ (or any LLVM-frontend language) to LLVM
bitcode, mark inputs as symbolic with `klee_make_symbolic()` or KLEE's
POSIX runtime (symbolic argv, files, stdin), and KLEE executes the program
over its reachable paths, solving each branch condition with an SMT solver.
For every explored path it writes a `.ktest` file holding the concrete
input that drives a native replay down exactly that path, and it reports
crashes, assertion violations, and memory errors together with a
reproducing input. KLEE analyses bitcode you compile yourself — for native
binaries you did not build, use a binary-level engine instead (e.g.
manticore).

## Typical use cases

### Generate one test input per path through a function

```
clang -I $KLEE_INCLUDE -emit-llvm -c -g -O0 -Xclang -disable-O0-optnone get_sign.c
klee get_sign.bc
```

Outcome: with `klee_make_symbolic(&a, sizeof(a), "a")` in the source, KLEE
explores all three branches (zero / negative / positive) and reports
`KLEE: done: generated tests = 3`, writing `klee-out-N/` with one `.ktest`
per path. Compile unoptimized (`-O0 -Xclang -disable-O0-optnone`) so KLEE's
own `--optimize` pass applies.

### Fuzz a program's command-line arguments

```
klee -posix-runtime password.bc -sym-arg 5
```

Outcome: the program's first argument is treated as a symbolic string of up
to 5 characters; every reachable parsing branch gets a `.ktest` with a
concrete argument — including the inputs that reach the "password correct"
path.

### Make program-opened files and stdin symbolic

```
klee -posix-runtime password.bc A -sym-files 1 10
```

Outcome: file `A` (as opened by the program) is backed by symbolic content
of up to 10 bytes, and KLEE generates the file contents needed to reach
each path. `-sym-stdin 10` does the same for standard input.

### Decode a generated test case

```
ktest-tool klee-last/test000003.ktest
```

Outcome: prints each symbolic object's name and concrete value as bytes,
hex, little-endian int/uint, and text — the values to replay natively or
add to a fuzzer corpus.

### Summarize coverage and solver statistics of a run

```
klee-stats klee-out-2
```

Outcome: tabular per-run statistics — instruction count, wall time,
instruction/branch coverage percentages, and solver time — for one output
directory or a list of `klee-out-*` runs.

*No corpus evidence — derived from upstream documentation.*
