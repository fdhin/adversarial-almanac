---
name: "goldeneye"
categories:
  - blackarch-dos
version: "28.792862f"
homepage: "https://github.com/jseidl/GoldenEye"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "jseidl/GoldenEye"
github-stars: 1471
github-pushed: "2023-11-16"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# goldeneye

> Python HTTP DoS test tool that exhausts web servers with keep-alive, no-cache requests for randomized URLs.

## What it's for

GoldenEye is an HTTP denial-of-service test tool by Jan Seidl. Its attack vector is HTTP Keep Alive combined with no-cache semantics: a pool of workers opens sockets, holds them open, and requests randomly generated URL paths with randomized headers and user agents, so neither caching layers nor simple filtering can collapse the load and the server's connection-handling capacity is what gets exhausted. Method (GET/POST/random), worker and socket counts, and user-agent lists are configurable. The project is no longer maintained (upstream archived, last release 2016-era), and it is intended strictly for security testing of systems you own.

### Basic keep-alive flood with defaults

One URL, stock settings — the smoke test.

```
./goldeneye.py http://target.example.com/
```

Outcome: starts 50 workers each maintaining up to 30 keep-alive sockets and prints running request counters until interrupted; on the target, the count of established connections climbs and stays pinned for as long as the tool runs.

### Scale workers, sockets and request methods

Turn the pressure up and mix request types.

```
./goldeneye.py http://target.example.com/ -w 100 -s 50 -m random
```

Outcome: 100 workers with 50 sockets each (5,000 concurrent connections instead of the default 1,500) alternate between GET and POST requests; watch the target's worker/thread pool or active-connection counters (`ss -tan | wc -l` on the server) to find where it degrades.

### Flood with a curated user-agent list

Replace the generated user agents with real browser strings.

```
./goldeneye.py http://target.example.com/ -u useragents.txt
```

Outcome: rotates the user agent on every request through the file's entries (one per line) instead of using randomly generated ones — useful when the target varies content per browser and you want the flood to look like distinct clients, and handy for testing user-agent-based rate limiting. Add `-n` for targets whose TLS certificate does not verify, `-d` for verbose debug output.

*No corpus evidence — derived from upstream documentation.*
