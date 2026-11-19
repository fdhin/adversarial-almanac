---
name: "arjun"
categories:
  - blackarch-scanner
version: "2.2.7.r5.gd1fb995"
homepage: "https://github.com/s0md3v/Arjun"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "s0md3v/Arjun"
github-stars: 6435
github-pushed: "2025-02-20"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# arjun

> HTTP parameter discovery suite: finds hidden GET/POST/JSON/XML parameters an endpoint accepts.

## What it's for

Arjun (s0md3v) discovers accepted-but-invisible HTTP parameters: it probes endpoints with
a dictionary of 25,890 parameter names (merged from CommonCrawl, SecLists and
param-miner) and detects which ones change the response, completing the sweep in roughly
50-60 requests thanks to its request-packing technique. It supports GET, POST, JSON and
XML injection points, imports targets from Burp exports, plain text files or raw request
files, exports results to JSON/text/Burp (`-oJ`/`-oT`/`-oB`), and copes with hostile
targets via threads, delays, rate limits and `--stable` mode. Reach for it before fuzzing
an endpoint: an undetected parameter (`admin`, `debug`, `id`) is often the actual attack
surface. It finds parameter names, not their valid values.

## Typical use cases

### Probe a single endpoint

The basic hidden-parameter hunt.

```
arjun -u https://api.example.com/v1/userinfo
```

Outcome: reports every parameter the endpoint responds to (e.g. `Parameters: [id, admin]`)
after sweeping the built-in dictionary.

### Probe with a specific method or body format

Hunt parameters beyond the query string.

```
arjun -u https://api.example.com/v1/userinfo -m JSON
```

Outcome: looks for parameters inside a JSON request body instead of a GET query — `GET`,
`POST`, `JSON` and `XML` are supported via `-m`.

### Batch targets and export results

Sweep a URL list and keep findings.

```
arjun -i targets.txt -oJ params.json -t 10
```

Outcome: scans every URL in the file (Burp exports and raw request files are auto
recognized) with 10 threads and writes discovered parameters per target to `params.json`.

### Hunt slowly against rate-limited targets

Avoid tripping WAFs and rate limits.

```
arjun -u https://api.example.com/endpoint --stable
```

Outcome: drops to a single thread with randomized 6-12 second delays between requests so
protected endpoints still complete; use `--ratelimit 2` instead for a fixed requests/sec
pace. A custom parameter wordlist can be supplied with `-w` (`small`/`medium`/`large`
built-ins or a file path).

*No corpus evidence — derived from upstream documentation (README and the project wiki's Usage page).*
