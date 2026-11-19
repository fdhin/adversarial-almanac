---
name: "persistencesniper"
categories:
  - blackarch-defensive
version: "v1.17.1.r0.g13006aa"
homepage: "https://github.com/last-byte/PersistenceSniper"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-15"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "last-byte/PersistenceSniper"
github-stars: 2152
github-pushed: "2024-12-11"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# persistencesniper

> PowerShell module that hunts persistence implants on Windows machines.

## What it's for

PersistenceSniper is a PowerShell module for blue teams, incident responders, and system administrators that sweeps a Windows machine for dozens of classes of attacker persistence — Run/RunOnce keys, services, scheduled tasks, WMI event subscriptions, Image File Execution Options debuggers, Winlogon helpers, NLDR DLL override paths, and many more (60+ getter checks behind one entry function). Findings print with technique, location, and file/registry evidence, can be exported to CSV, and can be diffed against a baseline so only newly-appeared persistence stands out. It enumerates only — it does not remove what it finds — and it targets Windows, so it runs from any PowerShell host against Windows machines (locally or via `-ComputerName`).

## Typical use cases

### Import the module and sweep the local machine

The default starting point for a hunt on the box you are on.

```
Import-Module PersistenceSniper
Find-AllPersistence
```

Outcome: Runs the default low-false-positive check set and prints each discovered persistence object — the technique, its location, and supporting details — to the console.

### Include the noisy checks

Trade precision for coverage of rarer implant locations.

```
Find-AllPersistence -IncludeHighFalsePositivesChecks
```

Outcome: Adds the harder-to-filter checks to the sweep; the output list gets longer and noisier but covers persistence techniques the default set skips.

### Hunt across multiple machines

Run one enumeration over several hosts instead of visiting each.

```
Find-AllPersistence -ComputerName ws01.example.com,ws02.example.com
```

Outcome: The enumeration runs against each named host in turn (needs appropriate privileges on the targets) and returns findings attributable per machine.

### Export findings to CSV

Keep a structured snapshot per host for comparison over time.

```
Find-AllPersistence -OutputCSV findings.csv
```

Outcome: All findings are written to findings.csv in addition to the console output, giving a per-host baseline you can archive.

### Diff against a known-good baseline

Reduce thousands of benign entries to the handful that are new.

```
Find-AllPersistence -DiffCSV baseline.csv
```

Outcome: Findings matching entries in baseline.csv are suppressed from the output, leaving only persistences that appeared since the baseline was captured — the short list worth investigating.

*No corpus evidence — derived from upstream documentation.*
