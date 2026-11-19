---
name: "jbe"
categories:
  - blackarch-decompiler
version: "0.1.1"
homepage: "https://set.ee/jbe/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jbe

> GUI editor for JVM class files — inspect and rewrite constants, methods, fields and bytecode instructions.

## What it's for

JBE (Java Bytecode Editor) opens compiled `.class` files in a tree of
constants, methods, interfaces and fields and lets you edit them directly:
rewrite constant-pool entries, add or remove constants, methods, interfaces,
fields and exception-table entries, and edit method bodies as standard JVM
opcode mnemonics. It is built on the jclasslib bytecode viewer, with Apache
BCEL used to verify and export the modified files. Changes are applied to the
class file immediately — there is no global save — but a "revert to original
class file" action restores the pristine version. It is a desktop application
from the author's site (Ando Saabas), not an actively developed project.

## Typical use cases

### Open a class file in the bytecode editor

Launch the editor to browse and patch a compiled class without source.

```
jbe
```

Outcome: the JBE window opens (the packaged wrapper runs
`java ee.ioc.cs.jbe.browser.BrowserApplication`); open a `.class` file from
the GUI to view and edit its constant pool, members and instruction lists,
then revert to the original file if an edit goes wrong.

*No corpus evidence — derived from upstream documentation (author site at
set.ee).*
