---
name: "winpwn"
categories:
  - blackarch-windows
version: "411.cba1769"
homepage: "https://github.com/S3cur3Th1sSh1t/WinPwn"
packaged-by: [blackarch]
blackarch-builddate: "2025-08-31"
build-system: windows
license: BSD
meta-checked: "2026-11-09"
github-repo: "S3cur3Th1sSh1t/WinPwn"
github-stars: 3707
github-pushed: "2025-08-28"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# winpwn

> PowerShell automation for internal Windows/AD penetration tests — recon, privesc and exploitation in one menu-driven module.

## What it's for

WinPwn (S3cur3Th1sSh1t) is a PowerShell script that automates the repetitive part of internal Windows and Active Directory assessments: it wraps well-known offensive PowerShell projects behind simple function names, adds automatic proxy detection (it was written for corporate environments where direct egress is blocked), and runs the collections in parallel where possible. After importing the module you get an interactive `WinPwn` menu plus individual functions for local recon (`Localreconmodules`), domain recon (`Domainreconmodules`), privilege-escalation checks (`Privescmodules`), credential theft (`kittielocal`), kernel-exploit and UAC-bypass launchers (`kernelexploits`, `UACBypass`), SMB relay attacks via Inveigh, and SYSTEM-shell helpers. Output lands in per-module result folders for review. Expect AV/AMSI friction — the README discusses bypass approaches — and use an offline variant (`Offline_Winpwn.ps1`) on isolated hosts.

## Typical use cases

### Run the interactive attack menu

Import the module and drive everything from one prompt.

```
Import-Module .\WinPwn.ps1
WinPwn
```

Outcome: launches the menu letting you pick attack categories (recon, privesc, creds, exploits); choices spawn the underlying tools in new windows, writing results to the current directory.

### Collect local-system reconnaissance

One function call that inventories the host for escalation and loot leads.

```
Import-Module .\WinPwn.ps1
Localreconmodules
```

Outcome: gathers installed/vulnerable software, shares, network config, groups and privileges, checks SMB signing/LLMNR/WSUS-over-HTTP weaknesses, hunts credentials in PowerShell event logs, browser stores, the registry and filesystem, and flags sensitive files (configs, RDP files, KeePass databases) into an output folder.

### Collect Active Directory reconnaissance

Same idea for the domain — the widest module.

```
Import-Module .\WinPwn.ps1
Domainreconmodules
```

Outcome: runs AD-wide collection — passwords in description fields, sensitive share files, unconstrained-delegation systems, a BloodHound report, MS17-010/BlueKeep scans, SQL discovery via PowerUpSQL, GPO auditing, ADRecon CSV reports and more — writing each tool's output to its results directory.

### Run the privilege-escalation check battery

Try the standard privesc checkers against the current host.

```
Import-Module .\WinPwn.ps1
Privescmodules
```

Outcome: executes itm4ns' Invoke-PrivescCheck, winPEAS, PowerUp all-checks, Sherlock, GPP-password discovery and DLL-hijack/weak-permission checks; review the generated reports for exploitable findings.

*No corpus evidence — derived from upstream documentation (S3cur3Th1sSh1t/WinPwn README function reference).*
