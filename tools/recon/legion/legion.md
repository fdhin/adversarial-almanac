---
name: "legion"
categories:
  - blackarch-recon
version: "64.703f2ad"
homepage: "https://github.com/carlospolop/legion"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "carlospolop/legion"
github-stars: 1037
github-pushed: "2026-10-22"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# legion

> Interactive enumeration console (carlospolop, HackTricks methodology) that drives nmap, hydra, dirsearch and friends against each service it finds, so you stop retyping the same per-service commands.

## What it's for

Legion wraps well-known open-source tools behind one console: you set a host, it tracks the discovered services and offers the matching commands, which can run automatically, semi-automatically or manually. Overlap is deliberate — several tools are pointed at the same service so nothing is missed. Options like `proto`, `host`, `port`, `username`/`password` and `intensity` (1 basic, 2 all checks, 3 brute force via hydra/metasploit/nmap) parameterize the generated command lines. It is interactive rather than scriptable, and several modules are loud, so only aim it at hosts you are allowed to test. Name collision to know about: Kali's `legion` package is a different GUI project (GoVanguard/Hackman238 lineage); this note covers the console tool BlackArch actually packages from carlospolop's repository.

## Typical use cases

### Launch the console and auto-scan a host

```
sudo legion
```

then inside the console:

```
set host 198.51.100.10
startGeneral
```

Outcome: the interactive console starts (root for nmap SYN scans and hydra); after `set host` and `startGeneral` the "General" workflow scans ports and services automatically and queues the per-service enumeration commands as discoveries land.

### Run one enumeration command by name

```
exec http_sqlmap
```

Outcome: executes exactly one named command against the current target — some per-service commands are reachable only this way. `protos` lists supported protocols, and the `help` internal command documents every console command and option.

*No corpus evidence — derived from upstream documentation (carlospolop/legion README).*
