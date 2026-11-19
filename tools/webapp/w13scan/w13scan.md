---
name: "w13scan"
categories:
  - blackarch-webapp
version: "430.432b835"
homepage: "https://github.com/boy-hack/w13scan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "boy-hack/w13scan"
github-stars: 1940
github-pushed: "2023-02-08"
github-archived: false
github-issues: 79
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# w13scan

> Passive and active Python 3 web vulnerability scanner with a rich open plugin library (semantic XSS, SQLi, request smuggling, info leaks, Struts2 and more).

## What it's for

W13Scan (boy-hack) is an open-source web-vulnerability discovery tool in Python 3 that supports two modes: passive scanning, where it runs as a proxy and analyzes your browsing traffic, and active scanning of a URL you hand it. Its plugin library is the draw: semantic reflected-XSS detection, error/boolean/time-based SQL injection, JSONP information leaks, HTTP request smuggling, Fastjson checks, .NET XSS, IIS parsing flaws, sensitive-file leaks (backups, debug files, JS secrets, repository leaks, directory listings), baseline/deserialization-parameter detection, command and code injection (including blind, across GET/POST/cookie), path traversal, Struts2 (s2-016/032/045), webpack source leaks and unauthorized-access checks. Plugins are fully open and extensible. Documentation and reports are mostly Chinese; HTML/JSON report output is built in.

## Typical use cases

### Passive-scan while you browse

Proxy your traffic through the analyzer.

```
w13scan -s 127.0.0.1:8888
```

Outcome: starts the passive-mode listener on 127.0.0.1:8888; configure your browser to use it as proxy and every request/response is analyzed by the plugin chain, with findings written to the output directory (HTML/JSON reports).

### Actively scan one URL

Test a known endpoint directly.

```
w13scan -u "http://target.example.com/page.php?id=1"
```

Outcome: runs the plugin chain against the URL and its parameters, printing detected issues and generating a report in the output directory.

### Scan a list of URLs at a chosen intensity

Batch with a payload level.

```
w13scan -f urls.txt --level 3 -t 20
```

Outcome: scans each URL in the file using payload level 3 (levels 1-5; higher sends more aggressive payloads) across 20 threads; --proxy routes the scan traffic through an upstream proxy (http or socks5).

No corpus evidence — derived from upstream documentation (README usage of boy-hack/w13scan).
