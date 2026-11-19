---
name: "procyon"
categories:
  - blackarch-decompiler
version: "0.6"
homepage: "https://github.com/mstrobel/procyon"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-06-23"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mstrobel/procyon"
github-stars: 1227
github-pushed: "2022-06-12"
github-archived: false
github-issues: 59
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# procyon

> Java metaprogramming suite whose decompiler front-end turns `.class` files and JARs back into readable Java source.

## What it's for

Procyon is a suite of Java metaprogramming libraries (reflection, expression trees, an experimental compiler toolset) plus a standalone decompiler front-end, `procyon-decompiler`, which is what the `procyon` command runs. The decompiler reads compiled Java class files and JAR archives and reconstructs source that handles Java 5+ constructs (generics, varargs, enhanced for loops) comparatively well. Output goes to the console unless an output directory is given. It is a decompiler only — it does not edit or re-export class files (use a bytecode editor for that), and upstream marks the decompiler as experimental. GUI front-ends such as Luyten embed the same engine.

## Typical use cases

### Decompile a JAR to the terminal

Read the source of every class in an archive at once.

```
procyon target.jar
```

Outcome: every class in target.jar is decompiled in turn and its reconstructed Java source is printed to the console.

### Write decompiled sources to a directory

Capture a whole archive as source files.

```
procyon -o src-recovered target.jar
```

Outcome: one `.java` file per class is written under src-recovered, mirroring the package structure, instead of printing to stdout.

### Decompile a single class file

Inspect one compiled class in isolation.

```
procyon Application.class
```

Outcome: prints the reconstructed Java source for that class only, including nested types by default.

### Dump raw bytecode instead of Java

Fall back to ground truth when decompilation is unclear or fails.

```
procyon -r Application.class
```

Outcome: prints an opcode-level raw bytecode listing rather than reconstructed Java; add `-v` for more detail (constant pool, local variable tables, type attributes).

### Tidy imports in bulk output

Make large decompiled dumps easier to read.

```
procyon -ci -o src-recovered target.jar
```

Outcome: repeated single-type imports from the same package are collapsed into wildcard imports throughout the recovered sources.

*No corpus evidence — derived from upstream documentation.*
