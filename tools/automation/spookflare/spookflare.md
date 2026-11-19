---
name: "spookflare"
categories:
  - blackarch-automation
version: "24.19491b5"
homepage: "https://github.com/hlldz/SpookFlare"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "hlldz/SpookFlare"
github-stars: 938
github-pushed: "2019-05-08"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spookflare

> Loader/dropper generator aimed at client-side and network-side detection bypass.

## What it's for

SpookFlare (hlldz, archived since 2019) generates loaders/droppers for
Meterpreter (EXE and PowerShell reverse HTTP/HTTPS), Koadic and Empire-era
C2 frameworks. Its evasion angle: per-build string obfuscation, Rijndael
encryption of the loader source, run-time .NET code compilation (CodeDOM)
so the real code only exists at execution time, character substitution,
and a documented technique that patches Metasploit's Meterpreter stage
with leading random bytes to defeat header-signature network IDS. It is a
generator only — it builds payloads you stage yourself. The bypasses are
2017-era; treat it as a study piece for loader/evasion history rather than
something that beats a current EDR.

## Typical use cases

### Start the payload generator console

```
python SpookFlare.py
```

Outcome: opens the interactive `SpookFlare >` prompt; the `help` command
prints the full command reference for the console.

### List the available payload loaders

```
SpookFlare > list
```

Outcome: prints the payload table — `meterpreter/binary` (EXE reverse
HTTP/HTTPS loader), `meterpreter/powershell`, `javascript/hta` (an HTA
delivered with an .HTML extension that runs a chosen command) and
`vba/macro` (an Office macro loader) — each with the ID you build from.

*No corpus evidence — derived from upstream documentation.*
