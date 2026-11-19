---
name: "justdecompile"
categories:
  - blackarch-windows
version: "22018"
homepage: "https://github.com/telerik/JustDecompileEngine/releases"
packaged-by: [blackarch]
blackarch-builddate: "2019-06-18"
build-system: windows
license: APACHE
meta-checked: "2026-11-09"
github-repo: "telerik/JustDecompileEngine"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# justdecompile

> The open-sourced decompilation engine of Telerik's discontinued free .NET decompiler JustDecompile.

## What it's for

JustDecompile was Telerik's free (freeware, closed-source) GUI .NET decompiler; both the product and its website are discontinued. What this package carries is the JustDecompile Engine — the C# decompilation core that powered the product, open-sourced by Telerik (Apache License 2.0, copyright 2011–2018) while the GUI stayed private. The engine is a class library, not a finished command-line tool: the repository's ConsoleRunner project is a console app built from source that exposes the engine's console functionality (notably decompiling an assembly into a Visual Studio project) and prints all available commands and switches when started. No runnable decompiler binary ships with the package, and the upstream repository is gone, so there is no stable documented command-line surface to reference — treat this as engine source for study or reuse rather than a working decompiler.

*No command-line binary ships with this package; this note is description-only. Grounding: archived upstream repository README (repo since deleted).*
