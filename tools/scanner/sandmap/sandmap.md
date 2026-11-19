---
name: "sandmap"
categories:
  - blackarch-scanner
version: "581.5be99d4"
homepage: "https://github.com/trimstray/sandmap"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "trimstray/sandmap"
github-stars: 1874
github-pushed: "2024-11-19"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sandmap

> Nmap front-end with a two-level interactive CLI exposing 31 modules and 459 predefined scan profiles.

## What it's for

sandmap wraps the Nmap engine in an interactive command line so common reconnaissance becomes a menu pick instead of a memorized flag string: the `cli(main)>` level sets session variables (destination, extra nmap parameters, report type, TOR routing), and each module (`use <module>`) opens a space where its scan profiles run by alias or ID. It supports the Nmap Scripting Engine including script arguments, can stack several profiles into one queued run, and routes scans through TOR via proxychains. It is a driver for nmap — it adds no probing logic of its own — and needs nmap (plus xterm/proxychains for the optional features), configured in `etc/main.cfg`, and root.

## Typical use cases

### Set a target and run a scan profile

Point a module's profile at a destination.

```
sandmap
set dest 198.51.100.10
use nse_ssl
init ssl-dh-params
```

Outcome: after `set dest`, `init` inside the module space runs the equivalent nmap command (`nmap --script ssl-dh-params 198.51.100.10`) and streams its live output, ending with `Result: pass` when the profile completes.

### Browse the module catalogue

Find a scanning technique without remembering nmap flags.

```
list
show host_discovery
search http
```

Outcome: `list` prints all 31 modules with profile counts and descriptions; `show <module>` tabulates each profile's alias, ID and exact nmap parameters (`show <module> <profile>` narrows to one); `search <key>` greps every profile's parameters.

### Stack several profiles into one scan

Queue multiple techniques against the same destination.

```
pushd ping_scan
pushd show
pushd init
```

Outcome: the command stack executes each pushed profile in turn against the session destination, then empties with `pushd flush`.

### Run plain nmap or TOR-routed scans

Escape hatch to the raw engine, or anonymized routing.

```
nmap -sn 198.51.100.10
set tor true
```

Outcome: the `nmap` command at the main level passes its arguments straight to the nmap binary; `set tor true` sends subsequent profiles through proxychains/TOR as set in `etc/main.cfg`.

*No corpus evidence — derived from upstream documentation (trimstray/sandmap README and wiki CLI/Modules pages).*
