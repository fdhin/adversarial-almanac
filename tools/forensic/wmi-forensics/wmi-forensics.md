---
name: "wmi-forensics"
categories:
  - blackarch-forensic
version: "11.0ab08dc"
homepage: "https://github.com/davidpany/WMI_Forensics"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "davidpany/WMI_Forensics"
github-stars: 320
github-pushed: "2020-08-14"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# wmi-forensics

> Two carving scripts that pull evidence out of raw Windows WMI repository OBJECTS.DATA files.

## What it's for

WMI_Forensics (David Pany, 2017, from his work at Mandiant/FireEye) targets the Windows WMI repository store — `C:\WINDOWS\system32\wbem\Repository\OBJECTS.DATA` (or the `FS\` variant) — without a live system or a full repository parser. PyWMIPersistenceFinder keyword-searches the raw file for FilterToConsumerBinding records, the mechanism behind a common malicious WMI persistence technique, and should also surface bindings deleted into unallocated space; CCM_RUA_Finder recovers SCCM software-metering RecentlyUsedApplication entries, an often-missed program-execution artifact. Expect keyword-carve fidelity rather than full object-model parsing: these scripts trade completeness for zero setup.

## Typical use cases

### Hunt WMI persistence bindings

Find event filters bound to consumers.

```
PyWMIPersistenceFinder.py OBJECTS.DATA
```

Outcome: prints each FilterToConsumerBinding as a `consumer-filter` pair with execution details (command line, script and so on); any binding not traceable to legitimate software is a persistence lead.

### Recover SCCM RecentlyUsedApplication records

Extract software-metering execution history.

```
CCM_RUA_Finder.py -i OBJECTS.DATA -o output.xls
```

Outcome: extracts RecentlyUsedApplication entries into a TSV file; the `.xls` extension makes Excel open it directly as a spreadsheet of metered application launches per user.

*No corpus evidence — derived from upstream documentation.*
