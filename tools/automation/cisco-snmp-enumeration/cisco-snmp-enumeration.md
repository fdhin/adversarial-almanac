---
name: "cisco-snmp-enumeration"
categories:
  - blackarch-automation
version: "10.ad06f57"
homepage: "https://github.com/nccgroup/cisco-snmp-enumeration"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/cisco-snmp-enumeration"
github-stars: 226
github-pushed: "2015-12-29"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco-snmp-enumeration

> Interactive Cisco SNMP attack chain: community-string brute force, config download, password cracking.

## What it's for

cisc0wn (NCC Group's cisco-SNMP-enumeration) is an interactive bash script that chains the classic Cisco IOS SNMP attack end to end, tested against IOS switches and routers: it checks whether SNMP answers, brute-forces the read-only and read-write community strings (the wordlist path is edited in the script header), enumerates IOS version, hostname, ARP table, routing table, interfaces and IP addresses with whatever string it finds, downloads the running config automatically when a RW string is discovered, then extracts enable/telnet passwords — decoding Cisco type-7 values in place and attacking type-5 enable secrets with John the Ripper. It drives Metasploit modules under the hood, so a working msfconsole is required. The project is unmaintained (last upstream work 2015, Backtrack/Kali-era) — treat it as legacy gear for old assessments, not a modern SNMP framework.

## Typical use cases

### Run the full SNMP attack chain interactively

One prompt-driven run covers check, brute force, enumeration and config extraction.

```
./cisc0wn.sh
```

Outcome: prompts for the target and other details, then walks the chain — SNMP check, community-string brute force, IOS/ARP/routing/interface enumeration, config download (RW string required), and password recovery with automatic type-7 decoding and John-based type-5 cracking — printing each finding as it lands.

*No corpus evidence — derived from upstream documentation.*
