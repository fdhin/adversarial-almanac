---
name: "brutespray"
categories:
  - blackarch-automation
version: "908.d4e361b"
homepage: "https://github.com/x90skysn3k/brutespray"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-06"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "x90skysn3k/brutespray"
github-stars: 2554
github-pushed: "2026-11-07"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 309
gh-activity-checked: "2026-11-09"
---

# brutespray

> Parallel credential brute-forcing across 40+ protocols, driven by Nmap/Nessus scan output.

## What it's for

brutespray takes service-discovery output you already have — Nmap GNMAP/XML, Nessus, Nexpose, JSON, or plain lists — and automatically attempts credentials against every discovered service in parallel: SSH, FTP, FTPS, Telnet, SMTP, IMAP, POP3, MySQL, PostgreSQL, MSSQL, MongoDB, Redis, VNC, SNMP, SMB, RDP, HTTP(S), LDAP(S), WinRM and more. It ships embedded default wordlists, an interactive terminal UI with a live Findings tab, per-module settings via `-m KEY:VALUE` (HTTP auth type, NTLM domain, SMB pass-the-hash), lockout-aware password spray mode, SOCKS5 proxy support, and checkpoint/resume after Ctrl+C. Reach for it when a scan has already mapped the attack surface and the next step is default/weak credential testing at scale; it does not discover ports itself, so feed it a scan file or explicit host.

## Typical use cases

### Brute-force every service found in an Nmap scan

Turn scan results straight into a credential attack.

```
brutespray -f nmap.gnmap -u admin -p passwords.txt
```

Outcome: parses the GNMAP file, identifies every supported service it references, and starts parallel attempts with username `admin` against the password list; valid credentials accumulate live in the TUI Findings tab.

### Attack one known service directly

Skip the scan file when the target service is already known.

```
brutespray -H ssh://198.51.100.10:22 -u root -p passwords.txt
```

Outcome: brute-forces only the single SSH service on 198.51.100.10 with the supplied user and password list.

### Sweep a whole CIDR range

Test one service type across a subnet.

```
brutespray -H ssh://198.51.100.0/24:22 -u root -p passwords.txt
```

Outcome: expands the CIDR into individual hosts and runs the same credential attack against each host's port 22 in turn.

### Verify a known user:password pair

A quick default-credential check with a fixed combo.

```
brutespray -H ssh://198.51.100.10:22 -C root:root
```

Outcome: attempts the exact `root:root` combination (no wordlist permutation) and reports whether it is valid.

### Preview which services a scan file exposes

See what a scan would be attacked on before committing.

```
brutespray -f nmap.gnmap -P -q
```

Outcome: lists the services brutespray recognizes in the scan file, without launching any credential attempts.

*No corpus evidence — derived from upstream documentation.*
