---
name: "threatcheck"
categories:
  - windows
homepage: "https://github.com/rasta-mouse/ThreatCheck"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: see repository
meta-checked: "2026-11-13"
github-repo: "rasta-mouse/ThreatCheck"
github-match: "exact"
github-stars: 1640
gh-checked: "2026-11-13"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# threatcheck

> Binary-split a payload against Defender or AMSI to pinpoint the exact bytes that trigger detection.

## What it's for

ThreatCheck (rasta-mouse, a modification of Matterpreter's DefenderCheck)
takes a file on disk — or fetched from a URL — and repeatedly splits it,
scanning each half with the selected engine, until it narrows down the
exact byte range the engine flags. It answers "which part of my tool is
detected?" during authorized red-team payload development: locate the
signature, then obfuscate, refactor or outsource that section. Windows
only — it calls the Defender scanning interface and AMSI in-process, so it
must run on a Windows host with those engines live. It does not modify,
bypass or clean payloads; it only localizes the trigger.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Find the detected bytes in a binary

Localize the Defender signature inside a compiled tool.

```
ThreatCheck.exe -f C:\Users\user\Downloads\tool.exe
```

Outcome: reports the target file size, walks the binary-search split, and
prints `[!] Identified end of bad bytes at offset 0x...` followed by a hex
dump of the offending region — the bytes to change or remove.

### Analyze a script through AMSI

Localize AMSI string detections in PowerShell or other script payloads.

```
ThreatCheck.exe -f C:\Users\user\Downloads\loader.ps1 -e AMSI -t Script
```

Outcome: scans the script content through the AMSI engine and prints the
hex dump of the flagged region (commonly a visible API or keyword string
inside the script), with the same offset reporting as binary scans.

### Analyze a payload straight from a URL

Skip the download step when iterating on a hosted build.

```
ThreatCheck.exe -u https://target.example.com/payload.exe
```

Outcome: fetches the file and runs the same split-scan analysis as `-f`,
using the default Defender engine; combine with `-e`/`-t` exactly as for
local files.
