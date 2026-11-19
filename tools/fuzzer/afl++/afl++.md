---
name: "afl++"
categories:
  - fuzzing
  - vulnerability
version: "5.00c-1"
homepage: "https://aflplus.plus/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-06-05"
build-system: make
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# afl++

> Coverage-guided fuzzer for C/C++ programs — the community-maintained fork of Google's AFL, pairing instrumenting compiler wrappers (afl-cc/afl-c++) with a mutation driver (afl-fuzz).

## What it's for

AFL++ (American Fuzzy Lop plus plus) finds bugs by coverage-guided mutation: you build the target with its instrumenting compiler wrappers so every execution reports which code was touched, then `afl-fuzz` evolves seed inputs toward new execution paths. It is the maintained fork of Michal Zalewski's AFL, stewarded by Marc "van Hauser" Heuse, Dominik Maier, Andrea Fioraldi and Heiko "hexcoder-" Eissfeldt — more speed, more and better mutations, custom module support. It drives programs that accept stdin or a file argument; binaries you cannot recompile need one of its qemu/frida/unicorn instrumentation modes, and network services need a small harness. Runtime tuning happens through `AFL_*` environment variables rather than long flag lists. The package is a native C toolchain — `afl-cc`, `afl-c++`, `afl-fuzz` and companion utilities.

## Typical use cases

### Instrument the target build

Compile the program under test with AFL++'s wrappers so executions become traceable.

```
CC=afl-cc CXX=afl-c++ ./configure --disable-shared
make clean all
```

Outcome: the resulting binaries carry AFL++ coverage instrumentation; `afl-fuzz` will report map coverage and path counts when executing them.

### Fuzz a stdin-reading program

Run the basic mutation loop over a set of seed files.

```
afl-fuzz -i seeds_dir -o output_dir -- /path/to/program
```

Outcome: starts the fuzzer status UI (cycles, paths, map density) and writes unique crashing inputs to `output_dir/crashes/` and slow inputs to `output_dir/hangs/`.

### Fuzz a file-reading program with a dictionary

Put `@@` where the target expects an input path.

```
afl-fuzz -i seeds_dir -o output_dir -x dictionary.txt -- /path/to/program @@
```

Outcome: AFL++ substitutes `@@` with a mutated file per execution and splices dictionary tokens into inputs — recommended when fuzzing verbose syntax such as SQL or HTTP.

### Replay a found crash

Reproduce a crash outside the fuzzer for triage.

```
cat output_dir/crashes/id:000000,* | /path/to/program
```

Outcome: re-feeds the exact crashing input to the target (e.g. under a debugger such as gdb) to confirm and investigate the fault.

*No corpus evidence — derived from upstream documentation.*
