---
name: "marc4dasm"
categories:
  - blackarch-disassembler
version: "6.f11860f"
homepage: "https://github.com/ApertureLabsLtd/marc4dasm"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ApertureLabsLtd/marc4dasm"
github-stars: 17
github-pushed: "2013-03-29"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# marc4dasm

> Disassembler for the MARC4, a 4-bit Harvard-architecture microcontroller used in smartcards.

## What it's for

marc4dasm turns a raw MARC4 firmware ROM dump into an annotated disassembly listing: instruction mnemonics with per-instruction comments, automatically generated ROM-address and RAM-variable label tables, and warnings for calls to impossible addresses. MARC4 cores (Atmel's 4-bit smartcard microcontroller family) show up in SIM cards and chip-card firmware, which is the reverse-engineering niche this tool was written for. It is a Python 2-era single script and treats the last two bytes of the input as a CRC. Known limitation from the author: ROM variables loaded via the TABLE command are addressed through the return stack and so are misread as code, which can desynchronize the listing after them.

## Typical use cases

### Disassemble a firmware dump

Produce the full annotated listing.

```
marc4dasm firmware.bin
```

Outcome: prints the disassembly to stdout with generated labels for ROM addresses and RAM variables, followed by the label tables and warnings for any call targets at invalid addresses.

### Print mnemonics only

Strip the address and raw-byte columns.

```
marc4dasm firmware.bin Q
```

Outcome: prints the same listing without address columns or original opcode bytes — just the annotated mnemonics, for a compact read-through of the code.

*No corpus evidence — derived from upstream documentation.*
