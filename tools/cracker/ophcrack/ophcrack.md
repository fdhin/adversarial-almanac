---
name: "ophcrack"
categories:
  - blackarch-cracker
version: "3.8.0"
homepage: "http://ophcrack.sourceforge.net"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-04-14"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "luisgg/ophcrack"
github-stars: 40
github-pushed: "2013-09-05"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ophcrack

> Rainbow-table Windows password cracker with a Qt GUI and a scriptable CLI.

## What it's for

ophcrack recovers Windows logon passwords from LM/NT hashes using precomputed rainbow tables (Oechslin's time-memory trade-off, developed at EPFL). It loads hashes from a pwdump-style file or directly from a Windows SYSTEM/SAM hive pair, cracks them against downloaded table sets, and presents plaintexts in a GUI table or writes them out in pwdump or CSV form. The free tables from the project site cover common XP and Vista-era alphanumeric passwords; larger table sets are sold separately and must be downloaded before any run. It is primarily a point-and-click tool for old Windows hash formats — for modern GPU cracking use hashcat. Command-line use requires `-g` to disable the GUI.

## Typical use cases

### Crack a pwdump file with mixed table sets

Headless run against a hash dump.

```
ophcrack -g -d /path/to/tables -t xp_free_fast,0,3:vista_free -f hashes.txt
```

Outcome: runs without the GUI, loads the NT/LM hashes from `hashes.txt` (pwdump format) and cracks them against sub-tables 0 and 3 of `xp_free_fast` plus all of `vista_free` under the tables directory; recovered plaintext:hash pairs are printed as the tables work through them.

### Dump-and-crack from a mounted Windows disk

Attack the SAM hive directly.

```
ophcrack -g -d /path/to/tables -t xp_free_small -w /mnt/windows/System32/config -o cracked.txt
```

Outcome: hashes are extracted from the encrypted SAM in the given directory (a Windows `System32/config` mounted from a partition that is not running) and cracked against the named tables; results are written to `cracked.txt` in pwdump format.

### Start the graphical cracker

The default point-and-click workflow.

```
ophcrack
```

Outcome: opens the Qt interface where hashes can be entered manually, imported from a pwdump file, extracted from an encrypted SAM directory, or dumped from the local machine (Local SAM requires administrator rights); tables are enabled via the Tables menu and the run starts with the Crack button, set the thread count under Preferences to the logical core count for best speed.

*No corpus evidence — derived from upstream documentation.*
