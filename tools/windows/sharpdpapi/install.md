---
tool: "sharpdpapi"
build-system: "dotnet"
upstream: "https://github.com/GhostPack/SharpDPAPI"
recipe: "upstream"
license: "BSD3-Clause"
---

# Installing sharpdpapi

> C# DPAPI triage toolkit: decrypt Windows masterkeys, Credential Manager blobs, Vaults, RDP .rdg files, Chrome/Edge data and certificates.

Source: hacktricks feed; upstream [`GhostPack/SharpDPAPI`](https://github.com/GhostPack/SharpDPAPI) (BSD 3-Clause).

## Build from source (upstream ships no binaries)

With Visual Studio 2022 (.NET 4.7.2 target, `System.EnterpriseServices`
reference per the README), open the solution and build Release:

```bash
git clone https://github.com/GhostPack/SharpDPAPI.git
# open SharpDPAPI.sln -> Release -> Build  (produces SharpDPAPI.exe)
# SharpChrome builds as a separate project in the same solution
```

The README also shows targeting other .NET versions and loading the
assembly in-memory from PowerShell
(`[Reflection.Assembly]::Load(...)` of a base64'd build) for use over
C2/PSRemoting.

## Precompiled third-party drops

[[ghostpack]] tracks a community repository of compiled GhostPack
binaries (including SharpDPAPI/SharpChrome) — third-party and untrusted
by definition; prefer building your own.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[sharpdpapi]]
