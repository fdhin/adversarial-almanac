---
tool: "seatbelt"
build-system: "windows"
upstream: "https://github.com/GhostPack/Seatbelt"
recipe: "upstream"
license: "BSD-3-Clause"
---

# Installing seatbelt

> C# host-reconnaissance: grouped, read-only Windows security checks.

Source: pentest-book feed; upstream [`GhostPack/Seatbelt`](https://github.com/GhostPack/Seatbelt).

## Build from source (official path)

The project deliberately ships no compiled binaries — build it
yourself. Seatbelt is built against .NET 3.5 and 4.0 with C# 8.0
features and is compatible with Visual Studio Community Edition: open
the project `.sln`, choose "Release", and build (to change the target
.NET framework version, modify the project settings and rebuild).

```powershell
git clone https://github.com/GhostPack/Seatbelt.git
cd Seatbelt
msbuild Seatbelt.sln /p:Configuration=Release
```

The compiled `Seatbelt.exe` lands in `Seatbelt/bin/Release/` and needs
only the matching .NET Framework runtime on the target host.

## Prebuilt nightly binaries

[[sharpcollection]] rebuilds Seatbelt from master every night for
.NET 4.0/4.5/4.7 in Any CPU/x64/x86 flavors. Review the binary in a
decompiler (dnSpyEx) before deploying it anywhere that matters.

## Not packaged by distros

No mainstream distro packages this (checked 2026-11-13) — it is a
Windows-only .NET tool.

Backlinks: [[windows]] · [[seatbelt]]
