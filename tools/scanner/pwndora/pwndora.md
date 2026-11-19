---
name: "pwndora"
categories:
  - blackarch-scanner
version: "1.ba7f6e6"
homepage: "https://github.com/alechilczenko/pwndora"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "alechilczenko/pwndora"
github-stars: 263
github-pushed: "2025-12-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# pwndora

> pwndora (now presented as "Kimi") — a high-concurrency Python scanner for IPv4 ranges that fingerprints internet-connected services and stores them in MongoDB behind a small API: a homebrew Shodan-style search engine.

## What it's for

Originally released as "pwndora — massive IPv4 scanner, find and analyze internet-connected devices in minutes, create your own IoT search engine at home"; the same repository (alechilczenko/pwndora) now presents the project as Kimi, an attack-surface discovery engine and portfolio piece. The engine is a threaded TCP/UDP prober (hundreds of concurrent sockets, configurable timeout) with banner parsing and GeoIP/ASN enrichment from bundled MaxMind databases, writing results to MongoDB; a companion REST API and mongo-express expose the stored results, with docker-compose files included for MongoDB and an optional Elasticsearch/Kibana stack. It is a scanner for ranges you are authorized to test, not a point-and-shoot CVE checker.

## Typical use cases

### Fast top-ports sweep of a range

Get a quick service map of a subnet.

```
python3 engine.py -r 198.51.100.0/24 -t 150 --top-ports
```

Outcome: 150 threads probe the 20 most-used ports of every address in the range; discovered services are parsed, geo/ASN-tagged, and written to MongoDB. Run from the `engine/` directory of the checkout.

### Scan specific ports at high concurrency

Target exactly the ports you care about.

```
python3 engine.py -r 198.51.100.0/24 -t 350 -c 80 443 8080 8443
```

Outcome: only the four listed ports are probed per host with 350 concurrent workers — the custom-ports mode (the README's older `-C` spelling is now `-c` in the source's argparse).

### Massive scan from a file of ranges, with logging

Sweep many networks unattended.

```
python3 engine.py -m ranges.txt -t 350 -l
```

Outcome: IPv4 ranges are read line-by-line from `ranges.txt` and scanned at 350 threads, and `-l` additionally writes a `logs/<timestamp>.log` file so progress survives a terminal disconnect.

*No corpus evidence — derived from upstream source (README plus `engine/core/parser.py` in alechilczenko/pwndora at HEAD; the BlackArch-packaged commit ba7f6e6 is this Kimi-era layout).*
