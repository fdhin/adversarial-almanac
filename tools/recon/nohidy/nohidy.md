---
name: "nohidy"
categories:
  - blackarch-recon
version: "67.22c1283"
homepage: "https://github.com/flipchan/Nohidy"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "flipchan/Nohidy"
github-stars: 14
github-pushed: "2018-02-06"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nohidy

> Interactive Python 2 "sysadmin assistant" menu: a numbered console that dispatches to small scripts for connections, logs, firewall, port lookup, and honeypot setup.

## What it's for

Nohidy (flipchan) is a 2014-era collection of Python 2 helper scripts behind one interactive console. Running `nohidy` (BlackArch installs a wrapper that executes `main.py` with Python 2) prints a numbered menu — "What do you want me to do master?:" — and the number you type launches the matching script: viewing current connections, log-file management and rootkit hunting, a Linux firewall helper, port lookup, tracing an attacker's IP, ARP spoofing/packet sniffing, Gmail/SSH-config helpers, honeypot installers, and network-card controls. It is a grab-bag convenience launcher rather than a coherent tool: the sub-scripts are independent, code quality is hobbyist, and everything assumes Python 2, so expect breakage on modern interpreters. Reach for it only as a curiosity or to reuse an individual sub-script directly from `/usr/share/nohidy/`.

## Typical use cases

### Launch the assistant menu

Drive the tool collection from its console.

```
nohidy
```

Outcome: prints the numbered task menu and waits at the "What do you want me to do master?:" prompt; entering a number (1–13) runs the corresponding sub-script in place — e.g. `1` lists your current network connections, `4` starts the firewall helper, `7` the ARP-spoof/sniffing script — and returns you to the prompt when it exits. Sub-scripts can also be run individually from the install directory.

*No corpus evidence — derived from upstream documentation (menu dispatch read directly from the packaged main.py; /usr/bin wrapper from the BlackArch PKGBUILD).*
