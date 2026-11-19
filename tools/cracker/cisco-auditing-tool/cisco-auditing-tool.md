---
name: "cisco-auditing-tool"
categories:
  - blackarch-cracker
version: "1"
homepage: "http://www.scrypt.net"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "foreni-packages/cisco-auditing-tool"
github-stars: 15
github-pushed: "2015-09-21"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco-auditing-tool

> Perl auditor for Cisco routers: guessable telnet passwords, SNMP community names, and the IOS history bug.

## What it's for

CAT (Cisco Auditing Tool) is an old-school Perl scanner for common Cisco router misconfigurations. For each host it walks a telnet password list (built-in defaults like `cisco`, `router`, `admin`, or your own via `-a`), guesses SNMP community strings (`public`/`private` by default or your list via `-w`), and can additionally probe the IOS history bug, which leaks logged-in users and command history. It ships as the binary `CAT` (usually also symlinked as `cisco-auditing-tool`) with its word lists under a shared directory. It does not go beyond these checks — no web or SSH testing — and the password phase is a short-list walk with a 2-second pause per guess, not a high-speed brute forcer.

## Typical use cases

### Audit a single router with defaults
```
CAT -h 198.51.100.10
```
Outcome: walks the built-in password list over telnet (port 23) printing `Password Found:` or `Invalid Password:` per guess, then tries the `public`/`private` SNMP communities printing `Community Name Found:` on success.

### Audit a host list with custom wordlists
```
CAT -f hosts.txt -w community.txt -a passwords.txt -l audit.log
```
Outcome: repeats the password and community-name checks for every host in `hosts.txt` using your lists instead of the built-ins, logging each attempt and finding to `audit.log` as well as the screen.

### Check for the IOS history bug
```
CAT -h 198.51.100.10 -i
```
Outcome: after the login and community checks, probes the IOS history bug and prints `Users Currently Logged In:` and `History Log:` sections exposing the router's session history — an information-disclosure finding on its own.

### Run quietly with results only in a file
```
CAT -f hosts.txt -l audit.log -q
```
Outcome: suppresses all screen output while the full per-host results still land in `audit.log` — useful for long unattended sweeps.

*No corpus evidence — derived from upstream documentation.*
