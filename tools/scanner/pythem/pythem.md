---
name: "pythem"
categories:
  - blackarch-scanner
version: "454.e4fcb8a"
homepage: "https://github.com/m4n3dw0lf/PytheM"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "m4n3dw0lf/PytheM"
github-stars: 1246
github-pushed: "2019-02-21"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pythem

> PytheM — an interactive Python 2 pentest console for man-in-the-middle, spoofing, sniffing, scanning and brute-force workflows on a LAN.

## What it's for

PytheM (m4n3dw0lf) is a root-run console (Python 2.7, GNU/Linux) that packs the moving parts of LAN attacks behind simple verbs: `arpspoof`, `dhcpspoof`, `dnsspoof`, `redirect`, `sniff`, `scan`, `dos`, `brute`, `webcrawl`, plus an `xploit` sub-console for exploit development (pattern, shellcode and fuzz helpers) and `pforensic` for pcap triage. Targets and options are set once with `set` and reused across commands, so multi-step MITM chains (poison → spoof → sniff) do not re-type parameters. It is squarely an attacking and teaching tool for lab networks, and the console has no scripting or headless mode.

## Typical use cases

### ARP-spoof MITM and sniff traffic

Poison the target–gateway relationship and watch the traffic.

```
sudo pythem
```

```
pythem> set interface wlan0
pythem> set gateway 198.51.100.1
pythem> set target 198.51.100.10
pythem> arpspoof start
pythem> sniff
```

Outcome: packet forwarding and iptables are configured, ARP spoofing is initialized between target and gateway, and `sniff` begins printing captured traffic (the wiki example filters with `core`) — plain HTTP content and credentials flow through the console.

### Add DNS spoofing on top of the MITM

Redirect the victim's domain lookups to your host while poisoned.

```
pythem> set target 198.51.100.10
pythem> set interface wlan0
pythem> set gateway 198.51.100.1
pythem> arpspoof start
pythem> dnsspoof start
```

Outcome: with the ARP poison running, `dnsspoof start` prompts for the domain to spoof (or `all`) and the redirect address (defaulting to your own IP), so the victim's lookups for that domain land on your hosted clone; pair with `sniff` to capture what the victim submits.

### Port-scan from the console

Quick tcp, arp or manual scans without leaving the session.

```
pythem> scan tcp
```

Outcome: runs a TCP port scan of the current target; `scan arp` sweeps the LAN by ARP instead, and `scan manual` prompts for an explicit comma-separated port list.

### Brute-force an SSH service

Drive a wordlist attack from the same session.

```
pythem> set target 198.51.100.10
pythem> set file wordlist.txt
pythem> brute-force ssh
```

Outcome: prompts for the username, then works `wordlist.txt` against the target's SSH login and reports any valid credentials found.

*No corpus evidence — derived from upstream documentation (README and the pythem wiki's Examples and Commands Reference pages; console transcripts quoted from the wiki).*
