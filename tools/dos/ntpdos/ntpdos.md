---
name: "ntpdos"
categories:
  - blackarch-dos
version: "4.3fe389b"
homepage: "https://github.com/sepehrdaddev/ntpdos"
packaged-by: [blackarch]
blackarch-builddate: "2019-10-04"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "sepehrdaddev/ntpdos"
github-stars: 10
github-pushed: "2019-10-04"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntpdos

> Proof-of-concept for distributed NTP reflection denial of service (CVE-2013-5211).

## What it's for

ntpdos demonstrates the classic NTP `monlist` reflection attack: it sends `monlist` (mode 7) requests to NTP servers with the victim's address spoofed as the source, so each small request triggers a large amplified response aimed at the victim from many servers at once. The tool scales the demonstration across lists of NTP servers and lists of targets with configurable process count and inter-packet delay. It exists to let defenders reproduce and measure the amplification factor of misconfigured NTP servers they operate — the underlying abuse was fixed server-side years ago (CVE-2013-5211, restricting monlist), and running it against third parties is traffic abuse. List files are plain text, one IP per line.

## Typical use cases

### Print the help and flag reference

Check the exact interface before building test scenarios.

```
ntpdos -H
```

Outcome: prints the usage banner with all options (`-t`/`-T` target, `-s`/`-S` server list, `-p` processes, `-d` delay) and the example invocations.

### Reflect off a list of NTP servers at one target

Measure how servers you control respond when used as reflectors.

```
ntpdos -t 198.51.100.10 -S servers.lst
```

Outcome: sends spoofed monlist requests (source = 198.51.100.10) to every server in servers.lst; capture on the target side to observe the amplified responses and compute the amplification factor per server.

### One server, many targets

Exercise multiple victims from a single reflector.

```
ntpdos -T targets.lst -s 198.51.100.20
```

Outcome: each IP in targets.lst receives reflected traffic sourced from 198.51.100.20 — useful in a lab to verify per-target filtering or rate limiting.

### Scale parallelism and pacing

Control how aggressive the demonstration runs.

```
ntpdos -T targets.lst -S servers.lst -p 200 -d 1
```

Outcome: the full cross product of targets and servers is driven with 200 parallel processes (default 80) and a 1 microsecond delay between packets (default 1000) — higher process count and lower delay both raise the offered load.

*No corpus evidence — derived from upstream documentation.*
