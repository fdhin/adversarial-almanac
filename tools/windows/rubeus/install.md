---
tool: "rubeus"
build-system: "windows"
upstream: "https://github.com/GhostPack/Rubeus"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing rubeus

> C# toolkit for raw Kerberos interaction and abuse on Windows AD.

Source: pentest-book feed; upstream [`GhostPack/Rubeus`](https://github.com/GhostPack/Rubeus).

## Build from source (official path)

The project deliberately ships no compiled binaries — build it
yourself. With Visual Studio (Community edition is sufficient), open
`Rubeus.sln`, select the Release configuration and build; or from a
terminal with the .NET SDK / msbuild on PATH:

```powershell
git clone https://github.com/GhostPack/Rubeus.git
cd Rubeus
msbuild Rubeus.sln /p:Configuration=Release
```

The compiled `Rubeus.exe` lands in `Rubeus/bin/Release/`. Rubeus
builds against .NET 3.5 by default with Visual Studio 2019 Community
Edition or newer; on hosts without 3.5, retarget to .NET 4 or 4.5 via
Project -> Rubeus Properties -> "Target framework" (README, "Compile
Instructions"). The README also documents building it as a class
library and running it in-memory through PowerShell
(`[Rubeus.Program]::MainString("...")`), including over PSRemoting.

## Prebuilt nightly binaries

[[sharpcollection]] rebuilds Rubeus from master every night for .NET
4.0/4.5/4.7 in Any CPU/x64/x86 flavors. Review the binary in a
decompiler before deploying it anywhere that matters.

## Not packaged by distros

No mainstream distro packages this (checked 2026-11-13) — it is a
Windows-only .NET attack tool.

Backlinks: [[windows]] · [[rubeus]]
