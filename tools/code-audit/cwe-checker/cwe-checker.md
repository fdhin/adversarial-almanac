---
name: "cwe-checker"
categories:
  - code-audit
homepage: "https://github.com/fkie-cad/cwe_checker"
github-repo: "fkie-cad/cwe_checker"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: LGPL3
github-stars: 1364
github-commits-year: 4
gh-checked: "2026-11-14"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# cwe-checker

> Static CWE-finder for ELF binaries — Ghidra-lifted, architecture-independent checks for the usual memory-corruption bug classes across x86, ARM, MIPS and PPC firmware.

## What it's for

cwe_checker (Fraunhofer FKIE) runs a suite of static analyses over ELF binaries and reports where the common weakness classes live: buffer overflows (CWE-119/125/787), format strings (CWE-134), integer overflows (CWE-190), use-after-free and double-free (CWE-416/415), NULL dereferences (CWE-476), dangerous functions (CWE-676), TOCTOU (CWE-367), and more. Binaries are disassembled by Ghidra into one common intermediate representation, so the same checks run on every architecture Ghidra understands — x86, ARM, MIPS, PPC — which is what makes it a natural first pass over extracted firmware images. Analysis techniques range from simple heuristics to abstract-interpretation data-flow analysis; the authors state plainly that false positives and negatives are expected — the output is a triage list of potentially vulnerable code paths, not proof. In practice its extras matter as much as the checks: JSON output for pipeline integration, a Ghidra plugin script that annotates findings back into the disassembler, FACT integration as a plugin, and experimental Linux-kernel-module and bare-metal (microcontroller memory-map config) modes. LGPL-3.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Scan a binary with the Docker image

```
docker run --rm -v /path/to/firmware:/input ghcr.io/fkie-cad/cwe_checker /input
```

Outcome: The full check suite runs over the mounted binary (or directory) and prints each CWE warning with address and affected function — no local Ghidra or Rust install needed.

### Scan with a local install

```
cwe_checker firmware.bin
```

Outcome: Same analysis as the container form via the locally built tool — the fastest iteration loop when scanning a whole firmware dump binary-by-binary.

### Scan without installing (nix)

```
nix run github:fkie-cad/cwe_checker -- firmware.bin
```

Outcome: Fetches and runs the current upstream in one command — a zero-install one-off audit or quick version test.

### Emit machine-readable findings

```
cwe_checker --json --quiet --out=findings.json firmware.bin
```

Outcome: CWE warnings written as parseable JSON with the console silenced — the shape a CI job or triage dashboard consumes.

### Run with a customized check configuration

```
cwe_checker --config=src/config.json firmware.bin
```

Outcome: Uses your edited copy of the config — e.g. teaching the dangerous-function or API checks about a vendor's custom libc — instead of the built-in defaults.

### Audit a bare-metal firmware image

```
cwe_checker --bare-metal-config bare_metal/stm32f407vg.json firmware.bin
```

Outcome: Runs the experimental bare-metal mode using an MCU memory-map configuration (an STM32F407VG example ships in the repo) — needed when the image has no ELF loader metadata to lean on.
