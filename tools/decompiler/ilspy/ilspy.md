---
name: "ilspy"
categories:
  - decompiler
homepage: "https://github.com/icsharpcode/ILSpy"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: MIT
meta-checked: "2026-11-13"
github-repo: "icsharpcode/ILSpy"
github-match: "exact"
github-stars: 27056
gh-checked: "2026-11-13"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# ilspy

> The open-source, cross-platform .NET assembly browser and decompiler — GUI desktop app plus a scriptable `ilspycmd` CLI.

## What it's for

ILSpy decompiles .NET assemblies (executables, libraries, NuGet packages)
back to readable C#, IL, or a whole compilable project. The desktop UI runs
on Windows, Linux and macOS (built on Avalonia) with an assembly tree,
search, hyperlink navigation and a metadata explorer; the same decompiler
engine is exposed as the `ilspycmd` dotnet tool, as Visual Studio / VS Code
extensions and as PowerShell cmdlets. Reach for it during code audit,
malware triage or dependency review whenever you need to read what a
compiled .NET binary actually does. It does not decompile native (non-.NET)
binaries. The earlier Linux-only GUI port is kept separately in this vault
as [[avaloniailspy]] (archived upstream; today's ILSpy itself is
cross-platform, so that port is legacy) — see that note for GUI-launch
details; the examples below use the `ilspycmd` automation surface.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Decompile an assembly to C# on stdout

Quickly read one binary without opening a GUI.

```
ilspycmd Service.dll
```

Outcome: prints the decompiled C# source of the assembly to standard
output; language version can be pinned with `-lv` (e.g. `-lv CSharp6`, default `Latest`).

### Decompile a whole project

Recover a compilable source tree from an assembly.

```
ilspycmd -p -o project-out/ Service.dll
```

Outcome: writes a `.csproj` plus decompiled `.cs` files for every type into
`project-out/`; BAML resources are decompiled to XAML in the same pass.
References the compiler cannot resolve locally can be pointed out with
`-r <directory>`.

### Decompile a single type or member

Jump straight to the class of interest in a big assembly.

```
ilspycmd -t Corp.Service.TokenValidator Service.dll
```

Outcome: prints only that fully qualified type's decompiled C#. A single
member can be pulled with `-m` using an XML doc id
(`M:System.String.Concat(System.String,System.String)`) or a metadata
token (`0x06000005`).

### Inspect IL instead of C#

See the exact instruction stream when the C# output looks optimized away.

```
ilspycmd -il Service.dll
```

Outcome: prints IL disassembly for the assembly; `--il-sequence-points`
adds debug sequence-point mapping.

### Inventory the assembly's types

List what a binary contains before reading any of it.

```
ilspycmd -l c Service.dll
```

Outcome: lists every class (`c`); other entity letters are `i` (interface),
`s` (struct), `d` (delegate) and `e` (enum) — combinable (`-l c,i`).

### Extract embedded resources

Pull configuration, scripts or BAML out of an assembly.

```
ilspycmd --list-resources Service.dll
ilspycmd --resource Strings.resources Service.dll > strings.txt
```

Outcome: the first command lists every embedded resource name; the second
extracts one by name to stdout — `.baml` entries are decompiled to XAML
automatically.

### Browse interactively in the GUI

Explore assemblies with search and navigation.

```
ILSpy -> File -> Open... -> pick Service.dll
```

Outcome: the assembly loads into the namespace/type tree; selecting a node
shows decompiled C#, and the search box finds types, methods and properties
across loaded assemblies. Assemblies can also be opened straight from a
NuGet package or a running process.
