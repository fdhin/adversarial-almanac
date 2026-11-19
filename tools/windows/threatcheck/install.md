---
tool: "threatcheck"
build-system: "dotnet"
upstream: "https://github.com/rasta-mouse/ThreatCheck"
recipe: "upstream"
license: "see repository"
---

# Installing threatcheck

> Binary-split a payload against Defender or AMSI to pinpoint the exact bytes that trigger detection.

Source: hacktricks feed; upstream [`rasta-mouse/ThreatCheck`](https://github.com/rasta-mouse/ThreatCheck).

## Prebuilt / releases

Check [upstream releases](https://github.com/rasta-mouse/ThreatCheck/releases) for compiled binaries.

## Build from source (Windows)

```bash
git clone https://github.com/rasta-mouse/ThreatCheck.git
```

Open the solution in Visual Studio and build Release (a .NET Framework
project), or `dotnet build` if your environment supports the target
framework. Run on the Windows host whose Defender/AMSI engines you want to
test against.

## Not yet packaged

No distro packages this tool — it is Windows/Defender-specific (checked
2026-11-13).

Backlinks: [[windows]] · [[threatcheck]]
