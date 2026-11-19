---
name: "xsstrike"
categories:
  - blackarch-webapp
version: "475.ab27955"
homepage: "https://github.com/s0md3v/XSStrike"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "s0md3v/XSStrike"
github-stars: 15247
github-pushed: "2025-04-26"
github-archived: false
github-issues: 93
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsstrike

> Advanced XSS detection suite with context analysis, an intelligent payload generator, fuzzing, crawling and WAF evasion.

## What it's for

XSStrike (by s0md3v / Somdev Sangwan, formerly under the UltimateHackers org) is a cross-site scripting detection suite. Instead of spraying payloads and checking reflections, it parses the response with hand-written HTML and JavaScript parsers, analyses the injection context, and crafts payloads that fit it; an integrated fuzzing engine then verifies them. On top of that it crawls (multi-threaded, powered by Photon), discovers hidden parameters (Arjun), detects WAFs and can evade them, scans for DOM XSS, flags outdated JavaScript libraries, and supports blind XSS and payload encoding. The project is GPL-3.0 licensed; the distro package installs the script as the `xsstrike` command.

## Typical use cases

### Scan a single URL

```
xsstrike -u "http://target.example.com/search.php?q=test"
```

Outcome: XSStrike analyses the parameter's reflection context, generates fitting payloads and reports which ones actually execute; without `-u` it starts an interactive prompt asking for the target.

### Test POST data

```
xsstrike -u "http://target.example.com/search.php" --data "q=test"
```

Outcome: the request body is used as the attack surface instead of the query string (`--json` treats the body as JSON).

### Crawl and scan a whole site

```
xsstrike -u "http://target.example.com" --crawl -l 3
```

Outcome: the target is crawled to the given level and every discovered parameter is context-analysed and tested, with results reported per URL.

### Fuzz for blind XSS while crawling

```
xsstrike -u "http://target.example.com" --crawl --blind
```

Outcome: the blind XSS payload is injected into every parameter visited during the crawl, so a later callback on your collector proves execution on pages you never saw directly.

### Route the scan through a proxy

```
xsstrike -u "http://target.example.com/search.php?q=test" --proxy http://127.0.0.1:8080 --delay 2
```

Outcome: all attack traffic is sent through the proxy (e.g. Burp) with a 2-second delay between requests, letting you observe or fine-tune each payload delivery.

*No corpus evidence — derived from upstream documentation (README and `xsstrike.py` argparse of s0md3v/XSStrike).*
