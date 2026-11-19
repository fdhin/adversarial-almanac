---
name: "dotpeek"
categories:
  - blackarch-windows
version: "2021.3.3"
homepage: "https://www.jetbrains.com/decompiler/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2022-02-27"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dotpeek

> JetBrains' free .NET decompiler and assembly browser (Windows GUI).

## What it's for

dotPeek is JetBrains' proprietary-but-free Windows decompiler for .NET. You load a compiled assembly (application exe/dll, framework library or NuGet package) and browse it as readable C# source, with search over symbols to locate the logic, strings or embedded values you are after — the typical analysis use is recovering the source of a .NET application to understand its behavior or find secrets. Unlike dnSpy it does not edit or debug assemblies, and it has no command-line interface; it is a desktop tool obtained from JetBrains. The packaged artifact is only the official Windows web installer, so running the tool itself requires completing that installation on Windows (see install.md); no runnable example can be given for this package's contents.
