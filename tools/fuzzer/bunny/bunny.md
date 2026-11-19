---
name: "bunny"
categories:
  - blackarch-fuzzer
version: "0.93"
homepage: "https://code.google.com/p/bunny-the-fuzzer/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2020-07-03"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# bunny

> Bunny the Fuzzer — Michal Zalewski's 2007 compile-time-instrumented feedback fuzzer for C programs, the conceptual ancestor of AFL.

## What it's for

Bunny (Michal Zalewski, 2007, Apache-2.0) is a closed-loop, protocol-blind fuzzer: a drop-in GCC wrapper (`bunny-gcc`) injects instrumentation at compile time so the fuzzer observes function calls, parameters and return values as inputs mutate, queues inputs that reach new call paths or new parameter values, and records faults — the same coverage-feedback idea Zalewski later productized in AFL (for current work use AFL++, see the [[afl++]] note in this category). The suite ships `bunny-gcc` (instrumenting compiler wrapper), `bunny-trace` (view a target's instrumented call tree) and `bunny-main` (fuzzing driver), plus the bunny-exec/bunny-flow helpers. Fuzzed output can also be delivered over TCP or UDP for network services, and non-instrumented binaries can be dumb-fuzzed for crashes alone. Era caveat: 0.93 (2008) is the final release, targeting 32-bit Linux/BSD/Cygwin — expect portability friction on modern systems.

## Typical use cases

### Build the target with instrumentation

Make a project's internals visible to the fuzzer.

```
CC=bunny-gcc ./configure
make
```

Outcome: the project's binaries compile with Bunny's call-path hooks — required before `bunny-main` can do feedback fuzzing; run `bunny-trace` on a built binary to confirm the hooks fire.

### Preview the instrumented call tree

See exactly what the fuzzer will observe.

```
bunny-trace ./program
```

Outcome: prints a live tree of instrumented calls with arguments and return values (main() → foo(1) → = 7 ...); an empty trace means the binary was not built with bunny-gcc.

### Fuzz a stdin-reading program

Run the feedback loop over a seed corpus.

```
bunny-main -i test/in_dir -o test/out_dir /path/to/app
```

Outcome: seeds from in_dir and runs the nine deterministic and random fuzzing stages, queueing inputs that reach new paths; the session log lands in out_dir/BUNNY.log and crash inputs in out_dir/FAULT*.

### Fuzz a file-input program

Handle targets that insist on a path argument.

```
bunny-main -i test/in_dir -o test/out_dir -f test/infile.jpg /path/to/app test/infile.jpg
```

Outcome: each mutated input is written to the file named by `-f` before the target is invoked with that path, so disk-reading programs can be fuzzed without stdin plumbing.

### Dumb-fuzz a non-instrumented binary

Use Bunny as a plain crash hunter when no instrumentation is possible.

```
bunny-main -i test/in_dir -o test/out_dir -d -8 /path/to/app
```

Outcome: a single round of brute-force fuzzing using all 256 byte values (`-8`) with crash detection only; for network targets, `-t host:port` (TCP), `-u host:port` (UDP) or `-l port` (wait for a connecting client) deliver the fuzzed output to a socket.

*No corpus evidence — derived from upstream documentation.*
