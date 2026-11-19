---
name: "ida-free"
categories:
  - blackarch-reversing
version: "8.4.240527"
homepage: "https://hex-rays.com/ida-free/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-01-05"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ida-free

> The no-cost edition of Hex-Rays' IDA disassembler for x86/x86-64 binaries.

## What it's for

IDA Free offers the core IDA recursive-descent disassembler at no cost: load a 32- or 64-bit x86 binary for Windows, Linux or macOS and you get function/string/cross-reference navigation, annotation, and saved analysis databases. Current releases also include a cloud-based x86/x86-64 decompiler and analysis saving; processor support stops at x86/x86-64, and the debugger, local decompilers and other advanced surfaces belong to the paid IDA Home/Pro editions. It is proprietary freeware — closed-source, license-restricted, published only from hex-rays.com (it has no official code repository; beware unofficial mirrors). The Linux install is GUI-first (`ida64`), but the install tree also ships the text-mode `idat64`, which supports IDA's documented batch switches for autonomous, script-driven analysis runs.

## Typical use cases

### Open a binary in the disassembler GUI

```
ida64 ./sample.bin
```

Outcome: IDA starts, loads the file, and after auto-analysis you can browse functions, strings and cross-references; the analysis is saved to an IDA database next to the file.

### Batch-disassemble without the GUI

```
/opt/ida-free/idat64 -A -B ./sample.bin
```

Outcome: IDA runs in autonomous batch mode — no dialogs — completes auto-analysis, saves the database and exports the disassembly listing to `sample.asm` before exiting. Paths reflect the packaged install tree; adjust for your install location.

### Run an analysis script headless

```
/opt/ida-free/idat64 -A -Sanalysis.idc ./sample.bin
```

Outcome: the IDC script executes inside autonomous IDA after the file loads — the documented batch-analysis pattern has the script wait for auto-analysis to finish, write its output (for example a text dump), then call `Exit` — so repeated triage steps can run unattended.

*No corpus evidence — derived from upstream documentation.*
