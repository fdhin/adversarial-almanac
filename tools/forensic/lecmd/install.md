---
tool: "lecmd"
build-system: "dotnet"
upstream: "https://github.com/EricZimmerman/LECmd"
recipe: "upstream"
license: "MIT"
---

# Installing lecmd

> LECmd — Windows .lnk shortcut parser for forensic examinations (CSV/JSON/XML/HTML export).

Source: HackTricks feed admission; upstream [`EricZimmerman/LECmd`](https://github.com/EricZimmerman/LECmd).

## Prebuilt binaries (recommended)

```text
.NET 10 build (recommended, cross-platform):
  https://download.ericzimmermanstools.com/net/LECmd.zip

.NET Framework 4.7.2 build (Windows only):
  https://download.ericzimmermanstools.com/LECmd.zip
```

Requirements: the [.NET 10 runtime](https://dotnet.microsoft.com/download/dotnet/10.0)
— run `LECmd.exe` on Windows, or `dotnet LECmd.dll` on any OS with the
runtime; or .NET Framework 4.7.2+ for the Windows-only build.

All of Eric Zimmerman's tools in one self-updating bundle:

```text
https://download.ericzimmermanstools.com/Get-ZimmermanTools.zip
```

## Build from source

```bash
git clone https://github.com/EricZimmerman/LECmd
cd LECmd
dotnet build LECmd.slnx -c Release
```

Builds `net10.0` and `net472`; shortcut parsing lives in the separate
[Lnk](https://github.com/EricZimmerman/Lnk) library (also on NuGet).

## Not packaged

Distributed as zip downloads from ericzimmermanstools.com; no distro packages
it. Checked 2026-11-13.

Backlinks: [[forensic]] · [[lecmd]]
