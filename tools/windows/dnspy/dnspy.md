---
name: "dnspy"
categories:
  - blackarch-windows
version: "6.1.8"
homepage: "https://github.com/dnSpy/dnSpy"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-10"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "dnSpy/dnSpy"
github-stars: 29652
github-pushed: "2020-12-20"
github-archived: true
github-issues: 185
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dnspy

> .NET decompiler, assembly editor and debugger for use without source code.

## What it's for

dnSpy is a Windows desktop tool that decompiles, edits and debugs .NET assemblies even when no source is available. It shows decompiled C#/VB for any exe or dll, lets you patch methods in place and save a modified binary, and runs assemblies under a full debugger — breakpoints in decompiled code, stepping, locals/watch windows, and saving runtime values such as decrypted byte arrays to disk. The classic workflow is to deobfuscate a packed binary with de4dot first, then open the cleaned assembly here to read or patch it. This package ships the original v6.1.8 release (repository archived December 2020); the project continues as the dnSpyEx fork with the same interface. It is a GUI application — usage below is launch plus in-window workflow.

## Typical use cases

### Open and decompile an assembly

Read the source of a compiled .NET binary.

```
wine /usr/share/windows/dnspy/dnspy.exe
```

Outcome: the IDE window opens; drag an assembly onto the window (or File → Open) and it appears as a tree of namespaces and types with decompiled C# code. Use the built-in search (classes, methods, strings) to locate credentials, config logic or license checks.

### Patch a method and save the binary

Change behavior without the original source.

```
wine /usr/share/windows/dnspy/dnspy.exe plugin.dll
```

Outcome: the assembly opens directly in the editor. Right-click a method → Edit Method (C#), modify the body (e.g. replace a password check), Compile, then File → Save Module to write the patched assembly that reflects your edit.

### Debug an assembly without source

Watch a program's real runtime values.

```
wine /usr/share/windows/dnspy/dnspy.exe app.exe
```

Outcome: the program loads under dnSpy's debugger; set breakpoints in the decompiled code, start with F5, step through and inspect locals and watch windows. Values shown in the variables windows (including decrypted strings or byte arrays) can be saved to disk for offline analysis, and self-decrypting assemblies can be debugged from their in-memory image.
