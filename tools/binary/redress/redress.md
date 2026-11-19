---
name: "redress"
categories:
  - blackarch-binary
version: "v1.2.2.r0.gfe38d96"
homepage: "https://github.com/goretk/redress"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-13"
build-system: go
license: AGPL
meta-checked: "2026-11-09"
github-repo: "goretk/redress"
github-stars: 1191
github-pushed: "2026-10-31"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# redress

> Recover symbols, packages, types, and source layout from stripped Go binaries.

## What it's for

Binaries compiled with the Go compiler keep rich metadata (build info,
moduledata, type information, pclntab function data) even when stripped.
redress uses that data to "re-dress" a stripped binary: it reconstructs the
compiler/build provenance, the package inventory, type definitions (structs,
interfaces, methods), and an estimated source-code tree layout with per-file
function lists and guessed line ranges — the skeleton a reverse engineer
otherwise lacks. It runs standalone or as an radare2 plugin via r2pipe, where
it plants thousands of function and type symbol flags into the open session.
It applies only to Go-compiler binaries, not to stripped C/C++ programs.

## Typical use cases

### Identify build provenance of a stripped Go sample

```
redress info ./malware_sample
```

Outcome: prints target OS and architecture, the exact Go compiler version and
release date, the build ID, GOROOT path, and the compile-time path of the
main package — enough to date and contextualize the sample.

### Enumerate the packages inside the binary

```
redress packages ./malware_sample --std --vendor --filepath
```

Outcome: lists project packages plus standard-library and vendored packages
with their original build-time folder paths, revealing which third-party
components (e.g. crypto miners, C2 libraries) were compiled in.

### Reconstruct the source code layout

```
redress source ./malware_sample
```

Outcome: prints a per-package, per-file projection of the original source
tree with each function and its estimated start/end line numbers — a map for
naming functions and prioritizing review in the disassembler.

### Dump struct and interface type definitions

```
redress types struct ./malware_sample --methods
redress types interface ./malware_sample
```

Outcome: emits Go source-level type declarations recovered from the binary,
with methods when requested, so heap allocations and interface calls in the
disassembly can be matched to concrete types.

### Annotate radare2 with recovered Go symbols

```
r2 ./malware_sample
#!pipe redress r2 init
#!pipe redress r2 type 0x01116c20
```

Outcome: the first command loads the binary in radare2; `redress r2 init`
creates symbol flags for every recovered function, method, and type and marks
type symbols so allocations are identifiable, while `r2 type <addr>` prints
the Go definition for the type at a given flag address.

*No corpus evidence — derived from upstream documentation.*
