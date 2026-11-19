---
name: "scanssh"
categories:
  - blackarch-scanner
version: "2.1"
homepage: "http://www.monkey.org/~provos/scanssh/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-16"
build-system: autotools
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "ofalk/scanssh"
github-stars: 37
github-pushed: "2026-05-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# scanssh

> Fast scanner for SSH servers and open proxies that also extracts service version numbers.

## What it's for

scanssh (originally written by Niels Provos under OpenBSD, now maintained by Oliver Falk at ofalk/scanssh) scans addresses and networks for running services — mainly to detect open proxies and to fingerprint SSH (plus Web and SMTP) server versions, printing the results as a list. Targets can be bare IPv4 addresses, `ipaddress/masklength` CIDRs, or ranges prefixed with `random(n[,seed])/` (sample n random addresses) and `split(s,e)/` (take slice s of e parts, so several hosts can scan one range in parallel); an exclude file keeps forbidden ranges out of every run. Detection and banner-grabbing only — no exploitation.

## Typical use cases

### Fingerprint SSH servers on a network

Find what SSH implementations a range runs.

```
scanssh -s ssh 198.51.100.0/24
```

Outcome: probes port 22 (the default) across the CIDR with the `ssh` scanner and prints each responding address with the version string it volunteered.

### Hunt open proxies

Detect SOCKS/HTTP/telnet proxies across custom ports.

```
scanssh -p -n 1080,8080,3128 198.51.100.0/24
```

Outcome: `-p` switches to proxy-detector mode and `-n` sets the ports; the available scanners are socks5, socks4, http-proxy, http-connect and telnet-proxy (combine explicitly with `-s socks5,http-connect`).

### Split and throttle a large scan

Distribute a range across hosts and pace the SYNs.

```
scanssh -r 50 -m 200 split(2,0)/198.51.100.0/24
```

Outcome: the `split(2,0)/` prefix takes the first half of the range so a second instance running `split(2,1)/` covers the rest; `-r` paces SYNs to one per 1/50 s and `-m` caps the scan queue at 200 hosts. Add `-u host:port,...` to route the scan through SOCKS proxies, and `-e excludefile` to skip reserved addresses.

*No corpus evidence — derived from upstream documentation (ofalk/scanssh `scanssh.1` man page, checked against the maintained fork of Niels Provos' original).*
