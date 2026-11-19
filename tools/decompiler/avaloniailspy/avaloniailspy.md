---
name: "avaloniailspy"
categories:
  - blackarch-decompiler
version: "v7.2.rc.r11.g21d4939"
homepage: "https://github.com/icsharpcode/AvaloniaILSpy"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-07-26"
build-system: other
license: MIT
meta-checked: "2026-11-09"
github-repo: "icsharpcode/AvaloniaILSpy"
github-stars: 1813
github-pushed: "2025-07-21"
github-archived: true
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# avaloniailspy

> Cross-platform Avalonia GUI port of the ILSpy .NET decompiler.

## What it's for

Browsing and decompiling .NET assemblies (executables, libraries, NuGet packages) into readable C# or IL on Linux and macOS, where the Windows-only WPF edition of ILSpy does not run. It loads an assembly into a tree of namespaces, types and members and decompiles any node on selection, with substring search for types/methods/properties, hyperlink-based navigation, and an Analyze window for finding usages. The upstream repository is archived and sees no further development: the project's own README points cross-platform users to the ILSpy VS Code extension instead. It is GUI-only — for scripted or headless decompilation the companion `ilspycmd` dotnet tool from the main icsharpcode/ILSpy project is the right instrument. Its MEF plugin interface is not compatible with ILSpy plugins.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the decompiler GUI

Start the assembly browser and pick files from the menu.

```
avaloniailspy
```

Outcome: the ILSpy main window opens with the default assembly list; opening a .dll or .exe from the menu loads it into the tree, and selecting any type shows its decompiled C# source.

### Open an assembly at startup

Pass assembly paths as arguments to land directly in the target. Use absolute paths: the packaged launcher switches to its install directory before starting the binary, so relative paths resolve against the wrong directory.

```
avaloniailspy /home/user/analysis/Service.dll
```

Outcome: the GUI starts with Service.dll already loaded and expanded in the assembly tree, ready to browse; `.nupkg` files passed this way are opened as package browsers.

### Start in IL view

Force the disassembly language instead of the default C# output.

```
avaloniailspy /language:IL /home/user/analysis/Service.dll
```

Outcome: every selected node is rendered as IL disassembly instead of C#; valid names are the same ones shown in the language dropdown (`C#`, `IL`), and an unrecognized name silently falls back to C#.
