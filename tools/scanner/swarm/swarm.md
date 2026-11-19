---
name: "swarm"
categories:
  - blackarch-scanner
version: "41.1713c1e"
homepage: "https://github.com/Arvin-X/swarm"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Arvin-X/swarm"
github-stars: 41
github-pushed: "2016-12-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# swarm

> Master/slave framework for distributing scan modules across many hosts.

## What it's for

Swarm (Arvin-X) is a distributed penetration-testing framework: a master
`swarm` process splits a job into subtasks distributed over a task queue,
slaves run `swarm-s` to execute them, and results land in MongoDB on the
master. Five modules ship — `domainsc` (subdomain bruteforce, dictionary or
full charset), `dirsc` (directory and file discovery with depth control and a
quick HEAD mode), `nmap` (wraps the nmap installed on each slave, options
passed through), `sitemap` (sitemap crawler) and `intruder` (HTTP fuzzing
with `@n@` attack-point markers) — and custom modules drop into the modules
directory. Slaves either run persistently or are woken on demand through a
listener you provide with nc/socat. It needs Python 2.6/2.7 and MongoDB;
development stopped in 2016, so treat it as a packaged legacy framework.

## Typical use cases

### Run a distributed subdomain scan

One slave listening, one master dispatching.

```
swarm-s -p 9090
swarm -m domainsc -t example.com -s 198.51.100.10 --dom-dict names.txt
```

Outcome: the slave listens on 9090; the master splits the dictionary-based
subdomain bruteforce of example.com into subtasks, ships them to the slave,
and findings accumulate in MongoDB while progress logs to the console.

### Distribute a directory scan across many slaves

Scale file discovery with target and slave files.

```
swarm -m dirsc -T targets.txt -S slaves.txt --dir-quick-scan -o swarm.log
```

Outcome: every URL in targets.txt is scanned by the slaves in slaves.txt
using HEAD requests (quick mode) instead of GET, with the run's log recorded
to swarm.log.

### Farm out port scans with the nmap module

Reuse nmap semantics, distributed.

```
swarm -m nmap -t 198.51.100.0/24 -s 198.51.100.10 --nmap-top-ports 100
```

Outcome: the /24 is split across the slaves, each of which runs its local
nmap against its share for the top 100 ports (`--nmap-ops` passes arbitrary
nmap options); results return to the master's MongoDB.

*No corpus evidence — derived from upstream documentation (Arvin-X/swarm
README and packaged module list).*
