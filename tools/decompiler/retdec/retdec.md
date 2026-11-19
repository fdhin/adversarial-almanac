---
name: "retdec"
categories:
  - blackarch-decompiler
version: "2161.04df6def"
homepage: "https://retdec.com/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "avast/retdec"
github-stars: 8668
github-pushed: "2026-05-26"
github-archived: false
github-issues: 470
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# retdec

> Retargetable machine-code decompiler based on LLVM: feeds ELF/PE/Mach-O/COFF/AR/Intel HEX/raw binaries through an analysis-and-unpacking pipeline and emits readable C, call graphs and control-flow graphs.

*No corpus evidence — derived from upstream documentation.*

## What it's for

RetDec (from Avast) decompiles machine code back into high-level source — C by default — across 32-bit x86/ARM/MIPS/PIC32/PowerPC and 64-bit x86-64/ARM64, reading ELF, PE, Mach-O, COFF, AR archives, Intel HEX and raw blobs. One command runs the whole pipeline: file and compiler/packer detection, optional unpacking, signature-based removal of statically linked library code, DWARF/PDB debug-info extraction, C++ RTTI/vtable reconstruction and symbol demangling, then LLVM-based recovery of functions, types and control flow. The driver is the compiled `retdec-decompiler` binary (older releases shipped the same interface as `retdec-decompiler.py`); the package also installs companion utilities such as `retdec-fileinfo` for static analysis and `retdec-unpacker`. Output is a readable approximation for auditing — not recompilable original source. The project is in limited maintenance mode: issues are handled slowly, but the tool is stable.

## Typical use cases

### Decompile a binary to C

```
retdec-decompiler firmware.elf
```

Outcome: runs the full pipeline and writes the decompiled source to `firmware.elf.c` (default output name is the input path plus `.c`), alongside a `.dsm` disassembly and a `.config.json` summarizing the analysis. Log lines show each phase; unreachable functions are dropped unless `-k` is given.

### Decompile a raw firmware image

```
retdec-decompiler -m raw -a mips -e big -b 32 --raw-section-vma 0x80000000 --raw-entry-point 0x80000100 -o blob.c blob.bin
```

Outcome: raw mode (`-m raw`) skips the executable-format loader and decodes the flat image directly; architecture (`-a`), endianness (`-e`), bit size (`-b`) and a load address plus entry point are all mandatory in this mode — the driver aborts naming the missing option. Produces `blob.c` reconstructed from the header-less blob.

### Decompile only chosen functions

```
retdec-decompiler --select-functions main,parse_header,dispatch -o selected.c sensor.elf
```

Outcome: only the named functions (comma-separated) are decompiled instead of the whole binary, which is much faster when auditing a few routines; add `--select-decode-only` to skip backend optimizations entirely (fastest, roughest output).

### Decompile an address range in a stripped binary

```
retdec-decompiler --select-ranges 0x401000-0x401200,0x402000-0x402150 -o probe.c probe.exe
```

Outcome: decompiles exactly the given address ranges — the symbol-free equivalent of `--select-functions`; useful when nothing is known about a binary except where the interesting code sits.

### Apply PDB debug information to a Windows binary

```
retdec-decompiler -p symbols.pdb -o agent.c agent.exe
```

Outcome: types and parameter information from the PDB are folded into the decompilation, so the emitted C carries named variables and structure layouts instead of anonymous registers and `void*`.

### Emit call and control-flow graphs

```
retdec-decompiler --backend-emit-cg --backend-emit-cfg -o graphs.c sample.elf
```

Outcome: besides the C output, a call graph for the whole module and a control-flow graph per function are emitted in Graphviz `.dot` format; with Graphviz installed they render directly to images for mapping program structure first.

### Triage a binary without decompiling

```
retdec-fileinfo sample.exe
```

Outcome: the companion analysis tool prints file format, architecture, sections, detected compiler and packer — useful to decide whether unpacking is needed before spending time in `retdec-decompiler`.

### Bound a long-running decompilation

```
retdec-decompiler --timeout 300 --max-memory 805306368 -o big.c huge.exe
```

Outcome: hard caps the run at 300 seconds and 768 MiB of memory (bytes; the default limit is half of system RAM), so a pathological binary fails cleanly instead of wedging the machine.
