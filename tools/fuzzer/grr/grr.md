---
name: "grr"
categories:
  - blackarch-fuzzer
version: "17.791ed5a"
homepage: "https://github.com/trailofbits/grr"
packaged-by: [blackarch]
blackarch-builddate: "2016-11-04"
build-system: cmake
license: Apache
meta-checked: "2026-11-09"
github-repo: "trailofbits/grr"
github-stars: 242
github-pushed: "2019-09-04"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# grr

> Trail of Bits' x86-to-amd64 binary translator built to emulate and fuzz DARPA Cyber Grand Challenge (DECREE) binaries.

## What it's for

GRR ("Granary Record/Replay") is a high-throughput dynamic binary translator created for the DARPA Cyber Grand Challenge, where it emulated and fuzzed DECREE challenge binaries. The workflow has two phases: `grrshot` snapshots a 32-bit DECREE binary right after `execve`, and `grrplay` replays test cases through the snapshot, optionally recording outputs or mutating inputs with an embedded Radamsa (`grrcov` ships alongside). It supports multiple communicating 32-bit processes inside one 64-bit address space, persists its code cache across runs, and handles self-modifying code — but it does not orchestrate a fuzzing campaign; an external system must direct it. The upstream repository is archived (since 2019), so this is a historical tool aimed at CGC/DECREE research, not general-purpose fuzzing.

## Typical use cases

### Snapshot a DECREE binary

Create the initial emulator image taken after `execve`.

```
grrshot --num_exe=1 --exe_dir=./binaries --exe_prefix=sample_ --snapshot_dir=/tmp/snapshot
```

Outcome: writes a snapshot of the matching binary (e.g. `./binaries/sample_00001`) into `/tmp/snapshot`; every later replay reuses it to skip translation overhead.

### Replay a test case

Run one input through an existing snapshot.

```
grrplay --num_exe=1 --snapshot_dir=/tmp/snapshot --persist_dir=/tmp/persist --input=crash_input
```

Outcome: replays `crash_input` inside the emulator and exits; `--persist_dir` holds the code cache that speeds up subsequent runs.

### Replay and record

Capture what the emulated binary emits for a given input.

```
grrplay --num_exe=1 --snapshot_dir=/tmp/snapshot --persist_dir=/tmp/persist --input=crash_input --output_dir=/tmp/out
```

Outcome: the replay's outputs are recorded under `/tmp/out` for offline inspection of the binary's reaction to the input.

### Replay with Radamsa mutation

Turn replay into a fuzzing loop.

```
grrplay --num_exe=1 --snapshot_dir=/tmp/snapshot --persist_dir=/tmp/persist --input=seed_input --output_dir=/tmp/out --input_mutator=inf_radamsa_spliced
```

Outcome: continuously mutates `seed_input` with the embedded Radamsa and replays each variant through the snapshot; `inf_`-prefixed mutators are non-deterministic and run indefinitely, so interrupt when enough coverage has been explored.

*No corpus evidence — derived from upstream documentation.*
