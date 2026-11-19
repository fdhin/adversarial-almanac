---
tool: "certify"
build-system: "dotnet"
upstream: "https://github.com/GhostPack/Certify"
recipe: "upstream"
license: "see repository"
---

# Installing certify

> Enumerate and abuse Active Directory Certificate Services (AD CS) misconfigurations — ESC1–ESC16 discovery, certificate requests, and CA/template manipulation.

Source: hacktricks feed; upstream [`GhostPack/Certify`](https://github.com/GhostPack/Certify).

## Build from source (upstream ships no binaries)

GhostPack deliberately does not release binaries. With Visual Studio 2022
(.NET 4.7.2 target):

```bash
git clone https://github.com/GhostPack/Certify.git
# open Certify.sln in Visual Studio -> Release -> Build
```

For in-memory use, base64-encode the built assembly and load it from
PowerShell:

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\Temp\Certify.exe")) | Out-File -Encoding ASCII C:\Temp\Certify.txt
[System.Reflection.Assembly]::Load([Convert]::FromBase64String("aa..."))
[Certify.Program]::Main("enum-templates --filter-enabled --filter-vulnerable".Split())
```

Over PSRemoting, use `[Certify.Program]::MainString("...")` in the wrapper
script (or `--out-file`) to capture output streams.

## Precompiled third-party drops

The vault note [[ghostpack]] tracks a community repository of compiled
GhostPack binaries if you cannot build — untrusted by definition, weigh
accordingly. There is no equivalent of a Certify 2.x binary release
upstream.

## Not yet packaged

No distro packages this tool (checked 2026-11-13).

Backlinks: [[windows]] · [[certify]]
