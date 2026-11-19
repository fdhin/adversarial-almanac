---
name: "routerhunter"
categories:
  - blackarch-scanner
version: "21.4da257c"
homepage: "https://github.com/jh00nbr/Routerhunter-2.0"
packaged-by: [blackarch]
blackarch-builddate: "2017-02-15"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "jh00nbr/Routerhunter-2.0"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# routerhunter

> RouterHunterBR 2.0 — scans IP ranges for home routers vulnerable to remote DNS-change (DNSChanger) exploits and rewrites their DNS servers.

## What it's for

An automated Python 2 scanner by Jhonathan Davi (jh00nbr) that sweeps IP
ranges, identifies vulnerable home routers and modems, and exploits
unauthenticated remote DNS-change flaws: it rewrites the device's DNS settings
to attacker-chosen servers. It covers a fixed catalogue of turn-of-the-decade
models — Shuttle Tech ADSL 915 WM, D-Link DSL-2740R / 2640B / 2780B / 2730B /
526B, and DSLink 260E (the last via default-credential brute force) — so it is
useful for auditing whether an estate still runs firmware with these specific
DNSChanger-class CVEs, and for demonstrating the impact of one. It is not a
general router vulnerability scanner; anything outside its exploit list is
invisible to it. The upstream repository is gone (verified via archive.org
snapshots; BlackArch still packages the last commit as the `routerhunter`
command, Python 2 with `requests`).

## Typical use cases

### Scan an address range for DNSChanger-vulnerable routers

Sweep a range and rewrite DNS on every vulnerable device found.

```
routerhunter --range 192.168.1.0-255 --dns1 198.51.100.10 --dns2 198.51.100.11 --threads 10
```

Outcome: probes each address for the supported router models, prints which
hosts are vulnerable, and on each hit replaces the device's DNS servers with
`198.51.100.10` / `198.51.100.11`; in an audit, point the DNS arguments at
resolver addresses you control and watch for the devices re-appearing in your
resolver's query log.

### Scan a wildcard IP block

Cover a whole external block defined with wildcards instead of an explicit
range.

```
routerhunter --startip 203.0.113.* --endip 203.0.113.* --dns1 198.51.100.10 --dns2 198.51.100.11 --threads 10
```

Outcome: expands the wildcard start/end pair into the set of addresses to test
and runs the same probe-and-exploit sweep as `--range`, reporting vulnerable
devices as they are found.

### Sample random addresses

Pull a limited number of random target IPs rather than a contiguous range.

```
routerhunter --randomip --limitip 10 --dns1 198.51.100.10 --dns2 198.51.100.11 --threads 10
```

Outcome: generates 10 random router addresses (`-rip` / `--randomip` with
`-lmtip` / `--limitip`) and tests only those, printing any vulnerable hits —
useful for a quick spot-check instead of a full sweep.

### Brute-force authenticated routers and change their DNS

Attack models that require a login (DSLink 260E) with default credentials.

```
routerhunter --range 192.168.1.0-255 --bruteforce --dns1 198.51.100.10 --dns2 198.51.100.11 --threads 10
```

Outcome: for devices in the range that demand authentication, the tool tries
standard default user/password combinations and, on success, applies the DNS
change — a device that succumbs needs both patched firmware and changed
credentials.

*No corpus evidence — derived from upstream documentation (the archived
jh00nbr/Routerhunter-2.0 README and usage listing via the Internet Archive, and
the BlackArch PKGBUILD).*
