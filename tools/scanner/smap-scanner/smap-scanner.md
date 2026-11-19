---
name: "smap-scanner"
categories:
  - blackarch-scanner
version: "0.1.12.r5.gfcf43c1"
homepage: "https://github.com/s0md3v/Smap"
packaged-by: [blackarch]
blackarch-builddate: "2025-06-07"
build-system: go
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "s0md3v/Smap"
github-stars: 3311
github-pushed: "2026-08-15"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# smap-scanner

> Passive Nmap replacement that pulls port, service, and vulnerability data from shodan.io's free API instead of touching the targets.

## What it's for

Smap (by s0md3v, Go; BlackArch installs the binary as `smap-scanner` —
upstream calls it `smap`) answers "what's open on these hosts?" without
sending a single packet to them: it queries shodan.io's free API (no
account or key needed) for internet-scanning data already gathered about
the targets, at ~200 hosts/second. It accepts Nmap-style arguments and
emits Nmap-compatible output formats, so it drops into existing Nmap
workflows. The tradeoffs are inherent to the source: results can be up to
seven days old, IPv6 isn't supported, and only internet-exposed hosts are
covered — internal ranges have no Shodan data. Beyond ports it surfaces
Shodan's service fingerprints, vulnerability tags, and banners. For
active confirmation it can hand its port candidates to a real local Nmap
run (`--nmap`).

## Typical use cases

### Passive lookups for a set of targets

Query without touching the targets.

```
smap-scanner 198.51.100.10 target.example.com
```

Outcome: Shodan data for each target is fetched (hostnames, IPv4, CIDR,
and `a.b.c.d-e` ranges all accepted) and an Nmap-style port/service table
prints — no packet is sent to the targets, making it safe for
pre-engagement recon and rate-limited environments.

### Batch targets from a file and filter ports

Scope a large list to the ports you care about.

```
smap-scanner -iL targets.txt -p 21-30,80,443
```

Outcome: targets are read one per line and the results are filtered to
the given port range (Shodan's ~4000 known ports are scanned by default),
so a long asset list collapses to the services in scope.

### Export in Nmap formats

Feed existing tooling.

```
smap-scanner target.example.com -oX output.xml
```

Outcome: `-oX`/`-oG`/`-oN`/`-oA` write Nmap's XML, grepable, and normal
formats (a `-` filename prints to the terminal), while `-oS` uses Smap's
own format — the only one carrying Shodan's vulnerability and tag data.

### Narrow an active Nmap scan with Shodan's ports

Let passive data pick the port range for a live scan.

```
smap-scanner --nmap -Pn -sV --version-light 198.51.100.10
```

Outcome: Smap first collects the union of ports Shodan has seen on the
target, then runs the local `nmap` once against the original target with
that reduced range (your other flags pass through to Nmap); faster than a
full-port active scan, at the cost of missing ports Shodan hasn't seen.

### Throttle the API usage

Tune concurrency for big batches.

```
smap-scanner --concurrency 5 -iL targets.txt
```

Outcome: raises the worker count from the default 3, trading Shodan API
pressure for throughput when sweeping thousands of targets.

*No corpus evidence — derived from upstream documentation (README).*
