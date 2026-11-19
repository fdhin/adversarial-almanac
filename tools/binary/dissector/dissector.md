---
name: "dissector"
categories:
  - blackarch-binary
version: "1"
homepage: "http://packetstormsecurity.com/files/125972/Coloured-ELF-File-Dissector.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dissector

> Colour-coded dissection of ELF internal structures: header, section and program headers, symbols, dynamic info, relocations and notes.

## What it's for

dissector is a small single-file C utility by Alejandro Hernandez (nitr0us)
that pretty-prints the internal data structures of an ELF file on Linux
(x86 and x86_64), colour-highlighting the values so a quick manual
inspection catches oddities faster than raw `readelf` output. It reads the
ELF header, section header table, program header table, symbol tables,
dynamic section, relocations and note segments — individually or all at
once — and can print raw numeric values instead of resolved names for
diffing. It is a reading aid, not a validator or disassembler: the author
explicitly notes there are no boundary checks, so a malformed ELF can
segfault it. Upstream is a single `dissector.c` published on Packet Storm;
the BlackArch package compiles it to `/usr/bin/dissector`.

## Typical use cases

### Dump every structure of an ELF at once

```
dissector -a /usr/bin/target
```

Outcome: prints the ELF header, section headers, program headers, symbol
table, dynamic information and notes in one pass — a full coloured overview
of the file's layout.

### Inspect the header and section table only

```
dissector -H -S /usr/bin/target
```

Outcome: prints just the ELF header and the section header table (names,
types, addresses and sizes), the usual first check for tampered or
anomalous section layouts.

### List the symbol tables

```
dissector -s /usr/bin/target
```

Outcome: prints the symbol table entries with their resolved names and
values — a quick view of what the binary defines and imports.

### Review dynamic linking data and relocations

```
dissector -D -R /usr/bin/target
```

Outcome: prints the dynamic section (dependencies, flags, needed libraries)
together with the relocation tables, showing exactly which imported
functions are patched at load time.

### Output raw numeric values for diffing

```
dissector -n -a /usr/bin/target
```

Outcome: every field is printed as its numeric value instead of a resolved
name, making two runs easy to diff mechanically (e.g. before/after
patching).

*No corpus evidence — derived from upstream documentation.*
