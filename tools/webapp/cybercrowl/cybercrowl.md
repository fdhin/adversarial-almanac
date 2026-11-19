---
name: "cybercrowl"
categories:
  - blackarch-webapp
version: "111.f7cac52"
homepage: "https://github.com/chamli/CyberCrowl"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-27"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "chamli/CyberCrowl"
github-stars: 24
github-pushed: "2019-02-05"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cybercrowl

> Minimal Python path scanner for quick web directory checks.

## What it's for

CyberCrowl checks which paths from a wordlist exist on a web root. It ships a small internal wordlist, accepts an external one, and adds request throttling, proxy support, randomized user agents and HTTP authentication (basic, digest, NTLM) — enough for a light sweep when a heavier fuzzer is unwarranted. It is a Python 2 era tool; it does not parse JavaScript or fuzz parameters.

## Typical use cases

### Quick scan with the internal wordlist

```
cybercrowl -u www.example.com
```

Outcome: probes the target with CyberCrowl's bundled wordlist and prints each path that exists as it is found.

### Scan an external wordlist with throttling

```
cybercrowl -u www.example.com -w wordlist.txt -d 1
```

Outcome: substitutes your wordlist and waits one second between requests to avoid blacklisting, reporting existing paths as they respond.

### Scan through a proxy with a randomized agent

```
cybercrowl -u www.example.com --proxy http://127.0.0.1:8080 --random-agent
```

Outcome: routes every probe through the given proxy (for example an intercepting proxy) and rotates the User-Agent header per request.

_No corpus evidence — derived from the packaged upstream source (flags verified against its argparse; the README's positional-URL usage line is stale — the source requires `-u`)._
