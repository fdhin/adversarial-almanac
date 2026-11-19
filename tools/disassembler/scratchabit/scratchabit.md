---
name: "scratchabit"
categories:
  - blackarch-disassembler
version: "571.e52ba4b"
homepage: "https://github.com/pfalcon/ScratchABit"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "pfalcon/ScratchABit"
github-stars: 413
github-pushed: "2020-11-25"
github-archived: false
github-issues: 29
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scratchabit

> Easily retargetable interactive disassembler in Python 3 with an IDAPython-compatible processor-plugin API — new CPU architectures are a plugin away.

## What it's for

ScratchABit (by Paul Sokolovsky) is an interactive incremental disassembler for static reverse engineering, dedicated to open-source driver and firmware RE. Two traits set it apart: it stores its disassembly database in easy-to-diff text formats (friendly to version control, unlike binary database blobs), and it is fully retargetable — it supports no CPU architecture by itself; processors are plugins written against the well-known IDAPython API. A simple x86 plugin ships built in, and since version 2.0 a Capstone-based plugin unlocks x86, ARM, MIPS, PowerPC, SPARC and more. It runs in a plain VT100/XTerm full-screen text UI, deliberately avoiding heavy dependencies. It is a static-analysis tool: no debugger, and decompilation lives in the related ScratchABlock project.

## Typical use cases

### Disassemble an ELF binary

Open a self-describing executable directly.

```
scratchabit example-x86_64.elf
```

Outcome: loads the ELF, opens the full-screen interactive UI on its code, and keeps the database as text files alongside; press F9 for menus (mouse works in XTerm-compatible terminals) and F1 for key-binding help. Quitting with unsaved changes offers to save the database.

### Disassemble a raw firmware image

Describe the memory layout in a .def file and load the raw binary.

```
scratchabit example-x86_64.def
```

Outcome: reads the `.def` definition file — CPU plugin via its `cpu` line, load address, and the defined memory areas — and disassembles the corresponding raw binary at those addresses. This is the standard route for header-less firmware blobs; write your own `.def` pointing at an IDAPython processor plugin to disassemble an otherwise unsupported architecture.

*No corpus evidence — derived from upstream documentation.*
