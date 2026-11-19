---
name: "thc-pptp-bruter"
categories:
  - blackarch-cracker
version: "0.1.4"
homepage: "http://www.thc.org"
packaged-by: [blackarch, kali]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "BlackArch/thc-pptp-bruter"
github-stars: 28
github-pushed: "2013-11-11"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thc-pptp-bruter

> Online MS-CHAPv2 password brute-forcer for PPTP VPN endpoints (TCP/1723).

## What it's for

Brute-forces PPTP VPN logins against MS-CHAPv2 authentication, the scheme used by nearly all internet-facing PPTP devices (including Cisco). It sidesteps Microsoft's anti-bruteforce measure — one caller-ID per second — by reusing the same LCP connection and caller-ID across many attempts (the "windows hack", enabled by default), reaching roughly 350 passwords/second on a LAN and 5–50/s over WAN. TCP/1723 must be open and the target must be reachable over GRE (IP protocol 47). Only CHAPv2 is supported (no CHAPv1), and one username is attacked per run.

## Typical use cases

### Pipe a wordlist on stdin

Classic invocation: passwords from stdin, default username `administrator`.

```
cat passwords.txt | thc-pptp-bruter 198.51.100.10
```

Outcome: attempts every word against the `administrator` account over parallel LCP slots; progress is printed per attempt and the working password is reported when authentication succeeds.

### Target a specific user with a wordlist file

Skip the shell pipe and name the account.

```
thc-pptp-bruter -u user -w passwords.txt 198.51.100.10
```

Outcome: same dictionary spray against the named account, reading candidates from the file instead of stdin.

### Throttle rate and parallelism

Cap attempts per second and concurrent slots for a fragile or monitored endpoint.

```
thc-pptp-bruter -u user -w passwords.txt -n 10 -l 20 198.51.100.10
```

Outcome: at most 20 passwords/second across 10 parallel authentication slots (defaults: 100/s, 5 slots), trading speed for a quieter run.

### Debug a target that rejects everything

Disable the windows hack and watch the protocol exchange in detail.

```
thc-pptp-bruter -v -W -u user -w passwords.txt 198.51.100.10
```

Outcome: verbose/debug output for each attempt with standard per-connection caller-IDs — useful to tell GRE/connectivity problems apart from authentication failures, especially on non-Microsoft endpoints.

*No corpus evidence — derived from upstream documentation.*
