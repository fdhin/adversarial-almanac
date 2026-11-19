---
name: "lief"
categories:
  - blackarch-disassembler
version: "0.17.3"
homepage: "https://github.com/lief-project/lief"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-17"
build-system: python
license: Apache-2.0
aliases: ["python-lief"]
meta-checked: "2026-11-09"
github-repo: "lief-project/lief"
github-stars: 5638
github-pushed: "2026-11-07"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 608
gh-activity-checked: "2026-11-09"
---

# lief

> Cross-platform library to parse, modify and re-serialize ELF, PE and Mach-O binaries from Python, C++ or Rust.

## What it's for

LIEF (Library to Instrument Executable Formats) gives one API over executable containers: ELF, PE, Mach-O and COFF, plus the Android formats OAT, DEX, VDEX and ART. It exposes parsed structure — sections, symbols, headers, relocations, entry point — through a format-agnostic abstraction so the same code triages any of them, and it supports modifying objects and writing the binary back to disk, which is what makes it a standard building block for binary instrumentation and packing/unpacking research. Extended builds add DWARF/PDB debug info, Objective-C metadata, dyld shared cache handling and disassembly. It is a library, not a CLI tool: you use it from Python (`import lief`), C++ or Rust; there is no `lief` command.

## Typical use cases

### Parse a binary and list its sections

Inspect the section layout of any supported format.

```
python3 -c "import lief; b = lief.parse('/usr/bin/ls'); [print(s.name, hex(s.virtual_address)) for s in b.sections]"
```

Outcome: prints each section name with its virtual address; `lief.parse` sniffs the container, so the identical call works on ELF, PE and Mach-O files.

### Triage a suspicious binary

Pull the quick indicators out of a sample.

```
python3 -c "
import lief
b = lief.parse('sample.exe')
print(b.format, hex(b.entrypoint))
print([f.name for f in b.imported_functions][:20])"
```

Outcome: prints the detected format, the entry point and the first twenty imported function names — typically enough to spot packers and known-bad API usage patterns.

### Modify and rebuild a binary

Round-trip a parsed executable back to disk.

```
python3 -c "
import lief
b = lief.parse('sample.bin')
b.write('sample.rebuilt.bin')"
```

Outcome: re-serializes the (optionally mutated) parsed object into a new executable that still loads; edits made to the parsed object before `write()` land in the rebuilt file.

### Read a PE Rich header

Extract the compiler/toolchain fingerprint of a Windows binary.

```
python3 -c "
import lief
b = lief.parse('program.exe')
rh = b.rich_header
print(rh.key) if rh is not None else print('no rich header')"
```

Outcome: prints the Rich header XOR key when present, or says there is none — a classic attribute for clustering samples by build environment.

### Inspect Mach-O dyld chained fixups

Look at how a modern macOS binary resolves its relocations.

```
python3 -c "
import lief
b = lief.parse('libfoo.dylib')
print(b.dyld_chained_fixups)"
```

Outcome: prints the chained-fixups table for an arm64 macOS library (or `None` when the format/feature is absent), showing the pointers the dyld loader rebinds at launch.

*No corpus evidence — derived from upstream documentation.*
