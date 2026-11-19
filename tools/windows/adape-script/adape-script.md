---
name: "adape-script"
categories:
  - blackarch-windows
version: "43.4d0b9ff"
homepage: "https://github.com/hausec/ADAPE-Script"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-02"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hausec/ADAPE-Script"
github-stars: 1127
github-pushed: "2022-12-07"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# adape-script

> One PowerShell script that runs the classic AD recon toolkit on a domain-joined Windows host and zips the results.

## What it's for

Consolidating a full Active Directory assessment into a single convenient (and loud) run. ADAPE.ps1 automates the collection modules a tester would otherwise launch one at a time: Inveigh (WPAD/LLMNR/NBT-NS/mDNS responder and hash capture), Invoke-Kerberoast, SharpHound collection, GPP password extraction (MS14-025), PowerUp local privilege-escalation checks, and PowerView's share discovery, exploitable-system mapping, file-server enumeration and domain policy dump. Each module's output lands in its own folder, zipped at the end; the sensitive-file search defaults to the string `password` but can be extended in the script. It pulls the modules from GitHub at run time, so it needs internet access and is explicitly not opsec-safe. Every module except BloodHound and PowerUp requires local administrator rights. It collects and surfaces attack paths (kerberoastable accounts, GPP passwords, escalation primitives) but does not exploit them.

*No corpus evidence — derived from upstream documentation (README and ADAPE.ps1 source).*

## Typical use cases

### Run every collection module at once

```
Set-ExecutionPolicy Bypass
.\ADAPE.ps1 -All
```

Outcome: runs Inveigh spoofing, kerberoasting, SharpHound, GPP-password checks, PowerUp and the PowerView sweeps in sequence, writing each module's output into a capture folder next to the script and zipping it when finished — the intermediate folders are deleted, the .zip remains.

### Run a selected set of modules

```
.\ADAPE.ps1 -GPP -PView -Kerberoast
```

Outcome: only the named modules execute — here GPP password extraction, the PowerView sweep (open shares, exploitable systems, file servers, domain policy) and kerberoast harvesting — with the same per-module output folders and final zip. Available switches: `-Inv`, `-Kerberoast`, `-Bloodhound`, `-GPP`, `-PrivEsc`, `-PView`, or `-All`.

### Collect without administrator rights

```
.\ADAPE.ps1 -Bloodhound -PrivEsc
```

Outcome: runs only the two modules that do not require elevation — SharpHound collection and PowerUp's local privilege-escalation checks — skipping the spoofing and share-search modules instead of failing partway through.
