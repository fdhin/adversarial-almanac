---
name: "pin"
categories:
  - blackarch-automation
version: "3.28.r98749"
homepage: "https://software.intel.com/en-us/articles/pin-a-binary-instrumentation-tool-downloads"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-07-12"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pin

> Intel's dynamic binary instrumentation framework: run any x86/x86-64 Linux (or Windows) binary under a "pintool" you write that can inspect and rewrite every instruction, memory access and image load as it executes.

## What it's for

Observing and modifying program execution at instruction granularity without recompiling or rewriting the target. Pin JIT-rewrites the target's code stream and calls your pintool's callbacks — on each instruction, basic block, trace, image load, or memory access — so you can count instructions, trace memory reads/writes, sniff syscall arguments, or build analysis prototypes that would otherwise need an emulator or debugger scripting. The kit ships a large set of ready-made example pintools in `source/tools/` (instruction counters, instruction/memory tracers, image-load loggers, malloc interceptors) that you build with `make` and run directly; custom analysis is C++ against the Pin API. It is an analysis framework, not a fuzzer or exploit tool, though it powers both in other projects (DynamoRIO is the comparable alternative; Intel's PT-based tooling has superseded Pin for some uses). Distributed only by Intel as a tarball from the download page (the homepage link) — there is no official GitHub repository — under Intel's license; this vault entry tracks the official Intel distribution, which is also what the BlackArch/AUR packages build from.

*No corpus evidence — derived from Intel's official documentation (the corpus hits for "pin" are unrelated: a compiled `pin.c`, a numeric PIN, and UI "pin/unpin" text).*

## Typical use cases

### Count the instructions a program executes

From the kit root, build and run the classic example pintool:

```
cd source/tools/ManualExamples && make obj-intel64/inscount0.so
pin -t obj-intel64/inscount0.so -- /bin/ls
```

Outcome: `/bin/ls` runs normally under the JIT, and the pintool writes the total dynamic instruction count (default output file `inscount.out`) when the program exits — the "hello world" of instrumentation. Use `obj-ia32/` instead of `obj-intel64/` for 32-bit targets.

### Pass options to the pintool

```
pin -t obj-intel64/inscount0.so -o inscount0.log -- /bin/ls
```

Outcome: everything between `-t <pintool>` and the `--` separator belongs to the pintool — here `-o` redirects the count to `inscount0.log` — while everything after `--` is the target program and its arguments. This `-t ... -- ...` split is the universal Pin invocation shape for any pintool.

### Trace every memory access

```
pin -t obj-intel64/pinatrace.so -- /path/to/target
```

Outcome: pinatrace logs each memory read and write (effective address, size, and the instruction's address) to `pinatrace.out` — the starting point for building taint-tracking, use-after-free analysis or cache-behavior experiments. The sibling `itrace.so` example records the full executed-instruction stream (`pin -t obj-intel64/itrace.so -- /bin/ls`), and `imageload.so` logs each image (executable/shared library) as it maps in.
