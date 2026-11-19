---
name: "goresolver"
categories:
  - reversing
homepage: "https://github.com/volexity/GoResolver"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: python
license: BSD-3-Clause
meta-checked: "2026-11-13"
github-repo: "volexity/GoResolver"
github-match: "exact"
github-stars: 117
gh-checked: "2026-11-13"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# goresolver

> Recovers function names from obfuscated Go binaries by matching control-flow graphs against freshly compiled references — reports import into IDA or Ghidra.

## What it's for

GoResolver (Volexity) de-anonymizes Go malware and tools whose symbols were stripped or obfuscated (garble-class protection). It combines two algorithms: direct Go symbol extraction from whatever the binary still exposes, and control-flow-graph similarity — it compiles reference samples from Go libraries with the matching Go toolchain and matches function graphs to recover names by structural identity. It handles PE, ELF and Mach-O for x86, AMD64, ARM and ARM64, and needs a base Go installation (1.20.6+) to build references. Results are JSON reports, and bundled IDA and Ghidra plugins apply the recovered names inside the disassembler. It is a static analysis aid — nothing here unpacks or runs the sample.

## Typical use cases

### Resolve a sample's symbols end to end

The standard run.

```
goresolver resolve -v go1.18 -o report.json sample.exe
```

Outcome: analyzes `sample.exe`, builds reference samples with the specified Go 1.18 toolchain, runs symbol extraction and graph comparison, and writes the recovered names to `report.json`; without `-v` it auto-detects the sample's Go version and, failing that, tests a range and picks the closest.

### Tune the match confidence

Filter out weak guesses.

```
goresolver resolve -t 0.95 sample.exe
```

Outcome: only CFG matches scoring above 0.95 count as resolved symbols (default threshold 0.9) — raise it when a wrong name is worse than no name; `-o` still writes the report for the disassembler import.

### Run one algorithm in isolation

Debug or speed up an analysis.

```
goresolver resolve -x -y sample.exe
```

Outcome: `-x` performs only static symbol extraction and `-y` only runtime-type parsing, skipping graph comparison (`-g` selects it alone) — best coverage comes from the default of all of them together.

### Manage the Go toolchains it builds with

Reference building needs the right compilers.

```
goresolver manage -i go1.18
```

Outcome: installs Go 1.18 into goresolver's own toolchain store without touching the system Go — `--list_available` and `--list_installed` enumerate what can be and is installed, `-u` uninstalls.

### Apply the results in IDA or Ghidra

From report to renamed functions.

Copy the plugin files from the repo's `Plugin/ida` or `Plugin/ghidra` directory (with their shared `common` files) into your IDA plugins directory or Ghidra extensions, then load `report.json` through the plugin.

Outcome: the recovered function names are applied to the corresponding addresses in the disassembler database, turning an anonymous call graph into readable, named code before you start manual review.

No corpus evidence — derived from upstream documentation.
