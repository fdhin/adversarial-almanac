---
name: "de4dotex"
categories:
  - blackarch-windows
version: "3.4.0.r4.gc21584f"
homepage: "https://github.com/GDATAAdvancedAnalytics/de4dotEx"
packaged-by: [blackarch]
blackarch-builddate: "2026-03-28"
build-system: windows
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "GDATAAdvancedAnalytics/de4dotEx"
github-stars: 304
github-pushed: "2026-10-23"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 128
gh-activity-checked: "2026-11-09"
---

# de4dotex

> Actively maintained fork of the de4dot .NET deobfuscator and unpacker.

## What it's for

de4dotEx is GDATA Advanced Analytics' fork of de4dot, collecting fixes and additions from across the community forks since the original project was archived in 2020. It keeps de4dot's command line and its strengths: decrypting strings and resources, undoing control-flow obfuscation, renaming obfuscated symbols, and unpacking twenty-odd obfuscator families. Two safety-relevant differences from the original: code paths that could execute parts of the input during dynamic deobfuscation (Babel.NET/CodeVeil `BinaryFormatter` use, JIT-hooking protectors) are disabled by default and can be re-enabled with `--unsafe-code-execution`, and it adds optional Docker, HTTP-API and MCP-server modes for sandboxed bulk deobfuscation. Like the original it cannot recover original symbol names, and untrusted samples still belong in a sandbox.

## Typical use cases

### Deobfuscate a single assembly

Clean one obfuscated file with an explicit output name.

```
de4dotex MyObfuscatedApp.exe -o CleanApp.exe
```

Outcome: detects the obfuscator, writes the deobfuscated assembly to `CleanApp.exe` (default naming appends `-cleaned` to the input name) and prints a report of what was removed. The BlackArch package installs the same CLI behind the `de4dotex` wrapper; upstream docs write it as `de4dot`.

### Detect the obfuscator only

Identify the protector without writing anything.

```
de4dotex -d file.dll
```

Outcome: prints the detected obfuscator family, or "Unknown obfuscator" for unsupported or unobfuscated files.

### Batch-deobfuscate a directory tree

Clean a whole application's assemblies together.

```
de4dotex -r ./input -ru -ro ./output
```

Outcome: recursively processes supported assemblies into `./output`, skipping unknown files, keeping cross-assembly references consistent after renaming.

### Enable dynamic decryption backends

Accept execution risk for protectors that must run to be stripped.

```
de4dotex --unsafe-code-execution file.exe
```

Outcome: re-enables the backends that deobfuscate by executing parts of the input (ILProtector, Agile.NET and similar). Only use on samples you are willing to execute; these paths need the .NET Framework 4.8 build, which the project's Wine container provides.

*No corpus evidence — derived from upstream documentation.*
