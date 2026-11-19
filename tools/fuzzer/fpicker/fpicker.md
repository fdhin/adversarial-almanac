---
name: "fpicker"
categories:
  - fuzzer
homepage: "https://github.com/ttdennis/fpicker"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-13"
github-repo: "ttdennis/fpicker"
github-stars: 298
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "MIT"
---

# fpicker

> Frida-based in-process fuzzing suite: AFL++-compatible coverage-guided fuzzing, standalone active fuzzing, or passive tracing of any process Frida can instrument.

## What it's for

fpicker brings coverage-guided fuzzing to targets that classic fork-server fuzzers struggle with: processes you can only reach by attaching (iOS apps, agents on locked-down platforms, network servers that must keep running), and functions you want to call in-place rather than through a file harness. Built on Frida's instrumentation (it grew out of the ToothPicker iOS/Bluetooth research), it runs wherever Frida does — macOS, iOS, Linux — in three modes: an AFL++ proxy that populates AFL's coverage bitmap in-process, a standalone active mode using Stalker call summaries with its own mutator (including external ones like radamsa), and a passive mode that traces a running target and records coverage-plus-input whenever new paths execute. Every target needs a small JavaScript harness (`Fuzzer` subclass naming the target function). It is not a black-box file fuzzer: without a function to instrument and call, there is nothing to fuzz — and targets reachable only over USB cannot use the shared-memory communication mode.

## Typical use cases

### Fuzz a function in-process under AFL++

```
afl-fuzz -i examples/test-network/in -o ./examples/test-network/out -- \
    ./fpicker --fuzzer-mode afl -e attach -p test-network -f ./examples/test-network/harness.js
```

Outcome: fpicker attaches to the running `test-network` process, instruments the harness's target function with Frida, and feeds AFL++'s generated inputs to it in-process while writing edge coverage back into AFL's bitmap — `afl-fuzz` drives mutation/queueing exactly as with a native target, and crashes surface in the AFL output directory.

### Fuzz standalone with an external client command

```
./fpicker --fuzzer-mode standalone -e attach -p server-process -f harness.js --input-mode cmd \
    --command "./client-send @@" -i indir -o outdir
```

Outcome: fpicker generates and mutates inputs itself and delivers each one by running `./client-send @@` (`@@` replaced by the payload file) against the attached server, while coverage is collected from the target function — the pattern for protocol servers where triggering the parser requires speaking the protocol from outside.

### Use a custom external mutator with shared-memory transport

```
./fpicker --fuzzer-mode active --communication-mode shm -e attach -p server-process -f harness.js \
    -i indir -o outdir --standalone-mutator cmd --mutator-command "radamsa"
```

Outcome: payloads are handed to `radamsa` for mutation and iterated in-process, with coverage returned over a shared-memory region instead of Frida's (slower, JSON-encoded) send channel — noticeably faster on targets where host and target can share memory, but unavailable when the device is attached via USB.

### Passively collect a corpus from live traffic

```
./fpicker --fuzzer-mode passive --communication-mode send -e attach -p server-process -o outdir -f harness.js
```

Outcome: fpicker injects no input of its own; it traces the attached process, and whenever a new basic-block path executes it stores both the coverage and the input that caused it — building a behavior-covered seed corpus from real sessions that can later seed an active campaign.

### Fuzz a process on a remote device

```
./fpicker --fuzzer-mode active -e attach -p test -D remote -o examples/test/out/ -i examples/test/in/ \
    -f fuzzer-agent.js --standalone-mutator cmd --mutator-command "radamsa"
```

Outcome: using Frida device selection (`-D remote`), the harness is injected into a process on another machine or phone and fuzzed from the host — no fuzzer binary needs to be deployed to the target device.

*No corpus evidence — derived from upstream documentation.*
