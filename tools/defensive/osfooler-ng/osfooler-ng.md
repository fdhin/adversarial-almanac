---
name: "osfooler-ng"
categories:
  - blackarch-defensive
version: "2.c0b20d6"
homepage: "https://github.com/segofensiva/OSfooler-ng"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "segofensiva/OSfooler-ng"
github-stars: 221
github-pushed: "2023-03-20"
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

# osfooler-ng

> NFQUEUE daemon that rewrites outgoing TCP/IP behavior so remote OS fingerprinting sees a fake OS.

## What it's for

OSfooler-ng intercepts the packets your host emits (via iptables NFQUEUE rules it installs itself) and rewrites the stack characteristics that fingerprinting engines key on, so active scanners like nmap and passive classifiers like p0f v2 read whichever OS you chose — drawn from those tools' own fingerprint databases. You can pick an nmap fingerprint, a p0f signature, or both at once; the emulation is transparent to local users and services, and is undetectable from the probing side. It defends the host it runs on; it is not a middlebox that can camouflage other machines behind it.

## Typical use cases

### Find a fingerprint worth emulating

Search both bundled databases instead of dumping them whole.

```
osfooler-ng -s playstation
```

Outcome: Prints every matching signature tagged by source database (e.g. `[nmap] "Sony PlayStation 3 game console"`); `-n` or `-p` alone lists the complete nmap or p0f v2 signature sets.

### Emulate a chosen nmap fingerprint

Make active OS detection (`nmap -O`) report a specific system.

```
osfooler-ng -i eth0 -m "Microsoft Windows 2000 SP4"
```

Outcome: Loads that exact nmap fingerprint, prints it as it is applied, and activates the NFQUEUE packet processors — probes against this host now classify it as Windows 2000 SP4.

### Emulate a p0f v2 signature

Control what passive stack fingerprinters conclude.

```
osfooler-ng -o "Windows" -d "XP bare-bone"
```

Outcome: Emulates the p0f genre "Windows" with details "XP bare-bone"; given only the genre (`-o` without `-d`) a variant is picked randomly each time a SYN is answered.

### Defeat active and passive fingerprinting together

Present one consistent fake OS to both engine types.

```
osfooler-ng -m "Microsoft Windows 2000 SP4" -o "Windows" -d "2000 SP4"
```

Outcome: Two processors start (Process-1 for nmap, Process-2 for p0f) and outgoing packets are rewritten to satisfy both signatures simultaneously.

### Refresh the nmap fingerprint database

Pick up newly published nmap OS signatures.

```
osfooler-ng -u
```

Outcome: Checks for a newer nmap OS fingerprint database and downloads it if available, making new entries emulatable.

*No corpus evidence — derived from upstream documentation.*
