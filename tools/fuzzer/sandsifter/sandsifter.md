---
name: "sandsifter"
categories:
  - blackarch-fuzzer
version: "2.8375e61"
homepage: "https://github.com/xoreaxeaxeax/sandsifter"
packaged-by: [blackarch]
blackarch-builddate: "2018-03-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "xoreaxeaxeax/sandsifter"
github-stars: 5084
github-pushed: "2024-02-20"
github-archived: false
github-issues: 64
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sandsifter

> x86 processor fuzzer that searches a CPU's instruction space for undocumented instructions and hardware bugs.

## What it's for

sandsifter (Christopher Domas, xoreaxeaxeax) audits x86 processors by systematically generating machine code and watching what the processor actually does with it. It drives a companion `injector` process through search algorithms — tunneling (the default, best speed/coverage trade-off), random, and brute force — and flags anomalies: instructions that execute but no disassembler knows (`--unk`), instructions whose real length disagrees with disassembly (`--dis`, `--len`), and crashes. This approach has uncovered undocumented instructions from every major vendor plus bugs in disassemblers, assemblers, emulators, and hypervisors. It needs root on bare metal (it maps a page at address 0 for accurate fault analysis), a 256-color terminal for its UI, and Capstone; a full scan runs from hours to days, after which `summarize.py` bins the anomalies into software bug, hardware bug, or undocumented instruction. It tests the instruction set itself, not programs. On BlackArch the scripts install as `sifter` and `summarize` with the injector at `/opt/sandsifter/injector`.

## Typical use cases

### Run a full processor audit

The standard scan of the whole instruction space.

```
sudo ./sifter.py --unk --dis --len --sync --tick -- -P1 -t
```

Outcome: scans the CPU using tunneling search (`-t`, injector flags after `--`) with prefix exploration limited to 1 byte (`-P1` — each extra prefix multiplies the search space ~10x); `--unk`/`--dis`/`--len` search for unknown instructions and length anomalies, `--sync` streams findings to `data/log`, `--tick` checkpoints progress. The terminal UI shows instructions under test and anomalies found; press `q` to stop with a text summary. Expect the run to take hours to days.

### Summarize a completed scan

Classify the anomalies the sifter collected.

```
./summarize.py data/log
```

Outcome: bins the raw log entries — typically millions of hits in a handful of groups — and attempts to classify each group as a software bug (disassembler/hypervisor at fault), a hardware bug in the CPU, or a genuinely undocumented instruction; this classified output is the actionable result of an audit.

### Fuzz a targeted opcode range

Test a hypothesis about one region of the ISA.

```
sudo ./sifter.py --unk --dis --len --sync --tick -- -t -i f0f0 -e f0f1 -D -P15
```

Outcome: restricts the search to instructions between `f0f0` and `f0f1` (e.g. repeated LOCK prefixes) while allowing duplicate prefixes (`-D`) and up to 15 prefix bytes (`-P15`) — the pattern for probing a suspected flaw in a specific CPU, emulator, or hypervisor region.

### Scan headless without the UI

Scriptable anomaly collection, no terminal interface.

```
sudo ./injector -P1 -r -0 | grep '\.r' | grep -v sigill
```

Outcome: runs the injector directly in random mode (`-r`), with `-0` mapping the null page so more instructions run to completion instead of segfaulting; the grep chain keeps only instructions where CPU and disassembler disagreed on length (`'.r'`) yet executed successfully (excluding `sigill`) — direct pipeable output for automated collection.

*No corpus evidence — derived from upstream documentation.*
