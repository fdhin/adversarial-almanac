---
name: "ncrack"
categories:
  - passwords
  - web
version: "0.7-3"
homepage: "https://nmap.org/ncrack/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2022-11-02"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "nmap/ncrack"
github-stars: 1230
github-pushed: "2024-04-14"
github-archived: false
github-issues: 74
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ncrack

> High-speed network authentication cracker from the Nmap project, with modules for 20+ protocols.

## What it's for

Ncrack audits live network services for weak or default passwords. It parallelizes aggressively — per-service connection limits, authentication tries per connection, timing templates T0–T5 — and adapts its behavior to network feedback, so one command can sweep a subnet of SSH/FTP/Telnet hosts or grind a single stubborn service. Modules cover SSH, RDP, FTP, Telnet, HTTP(S), Wordpress, POP3(S), IMAP, CVS, SMB, VNC, SIP, Redis, PostgreSQL, MQTT, MySQL, MSSQL, MongoDB, Cassandra, WinRM, OWA and DICOM. Targets are specified Nmap-style (hostnames, CIDR, octet ranges) and Nmap XML output can be imported directly, which makes scan-then-crack a two-command workflow. It attacks live authentication, not offline hash files — for those use John the Ripper or hashcat.

## Typical use cases

### Crack a single SSH service

Verbose run against one target with one user.

```
ncrack -v --user root ssh://target.example.com
```

Outcome: attempts the username (plus the built-in default credential pairings) against SSH on port 22; found credentials print as `Discovered credentials for ssh on target.example.com 22/tcp:` followed by `target.example.com 22/tcp ssh: root <password>`, with `-v` adding per-connection progress.

### Crack every service found by an Nmap scan

Feed Nmap output straight in with global limits.

```
ncrack -v -iX nmap-scan.xml -g CL=5,to=1h
```

Outcome: cracks all services discovered in the XML with the global options applied everywhere — at most 5 parallel connections per service (`CL`) and at most one hour per service (`to`) — keeping a large unattended run bounded. `-iN` accepts Nmap's normal output format the same way.

### Sweep a subnet with username and password files

Apply a service list across many hosts at once.

```
ncrack -T4 -U users.txt -P passwords.txt -p ssh,ftp,telnet 198.51.100.0/24
```

Outcome: runs every host in the /24 against SSH, FTP and Telnet with the timing template T4; each discovered credential prints on its own `host port service: user pass` line. Add `-f` to quit each service after its first found credential.

### Go easy on a fragile server

One connection, linear credential order.

```
ncrack --stealthy-linear -U users.txt -P passwords.txt rdp://198.51.100.10
```

Outcome: overrides all other timing options and tries credentials through a single connection per pass — the safe mode for brittle services such as RDP on old Windows hosts that drop parallel connections. Alternatively cap parallelism per service type with options like `-m rdp:CL=1,cd=30`.

*No corpus evidence — derived from upstream documentation.*
