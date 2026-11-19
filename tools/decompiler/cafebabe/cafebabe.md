---
name: "cafebabe"
categories:
  - blackarch-decompiler
version: "0.1.2"
homepage: "https://grax.info/"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "GraxCode/Cafebabe"
github-stars: 271
github-pushed: "2019-09-05"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cafebabe

> GUI Java bytecode editor with a built-in decompile view.

## What it's for

Editing compiled Java class files at the bytecode level. Load .class files, patch instructions, access flags and constants, and export without hand-managing stack frames — Cafebabe regenerates the stack map frames automatically at export, with no library jars needed at runtime. Method descriptors are reversed to Java syntax, node access can be toggled without knowing raw JVM access values, try/catch blocks and jump labels are handled in the editor, and built-in help explains each opcode. A decompiled view is provided by the embedded CFR 0.145, and control-flow graphing is inherited from JByteMod. The name refers to 0xCAFEBABE, the magic number of the Java class file format. The packaged release is "Cafebabe Lite" 0.1.2 (2019); the upstream README warns that this version is not finished and some listed features may be missing. It is purely graphical — `main()` takes no arguments, so all work happens in the Swing UI.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the bytecode editor

Start the editor and open a class file for patching.

```
cafebabe
```

Outcome: the editor window opens (WebLaF look and feel); loading a .class file via the file dialog shows its constant pool, fields and methods in the multiview editor, several class files can be edited at once, and exporting writes the modified class with freshly regenerated stack frames.
