---
name: "sn1per"
categories:
  - blackarch-automation
version: "722.a5450bc"
homepage: "https://github.com/1N3/Sn1per"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: node
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/Sn1per"
github-stars: 11527
github-pushed: "2026-10-27"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 18
gh-activity-checked: "2026-11-09"
---

# sn1per

> Automated pentest recon scanner that runs a full workflow per target and stores it as loot.

## What it's for

Sn1per automates the reconnaissance-to-exploitation workflow: OSINT, port
scanning, service and web enumeration, vulnerability scanning and optional
brute force, orchestrated as one run against a single target or in bulk
against a target file. Every result lands in per-workspace loot directories
(`/usr/share/sniper/loot/<workspace>/`) that can be listed, reimported and
exported later. Reach for it when you want broad, repeatable, automated
coverage of many targets; expect noisy output whose findings still need
manual vetting. The command is `sniper`; the BlackArch package installs a
`sn1per` launcher of the same name.

## Typical use cases

### Run a full automated scan of one target

```
sniper -t target.example.com -m normal -o -re
```

Outcome: normal mode (active + passive scan of the target and its open
ports) with OSINT (`-o`) and extra recon (`-re`) enabled; results
accumulate under the workspace loot directory for the target.

### Keep the scan non-intrusive

```
sniper -t target.example.com -m stealth -o -re
```

Outcome: stealth mode — quick, mostly non-intrusive enumeration chosen to
avoid tripping WAF/IPS, with the same OSINT and recon add-ons.

### Discover live hosts across a CIDR into a workspace

```
sniper -t 198.51.100.0/24 -m discover -w corpnet
```

Outcome: walks the range, runs Sn1per against every live host found, and
files everything under the `corpnet` workspace for follow-up targeted
scans.

### Scan a whole target list

```
sniper -f targets.txt -m airstrike
```

Outcome: airstrike mode runs open-port enumeration and basic
fingerprinting against every entry of `targets.txt`; swap in `-m nuke`
(`sniper -f targets.txt -m nuke -w corpnet`) for a full audit with
brute force, full port scan, OSINT and recon enabled on every target.

### Manage workspaces and loot

```
sniper --list
sniper -w corpnet --export
```

Outcome: `--list` prints existing workspaces; `--export` bundles the
workspace's loot for reporting. `--status` shows the state of a running
scan, and `sniper -u` updates the tool itself.

*No corpus evidence — derived from upstream documentation.*
