---
name: "autobrowser-git"
categories:
  - webapp
version: "20161027.r24"
homepage: "https://github.com/El3ct71k/AutoBrowser"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "El3ct71k/AutoBrowser"
github-stars: 49
github-pushed: "2016-10-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# autobrowser-git

> Nmap-driven service screenshotter: scans (or ingests an nmap XML report), probes every HTTP/S port with a headless browser, and files a screenshot plus JSON report of each web service into a project folder.

*No corpus evidence — derived from upstream documentation (README plus the packaged AutoBrowser.py argparse definitions).*

## What it's for

Turning "here are 200 open web ports" into something a human can triage. AutoBrowser wraps nmap and a headless web browser (PyQt4/ghost.py) into one step: it either runs nmap itself (`scan`) or consumes an existing `nmap -oX` report (`analyze`), extracts every http/https service, loads each in a browser, and records what the page looked like plus the request results. The deliverable is a per-project directory of screenshots and JSON — ideal evidence pack for report writing, and a fast way to spot admin panels and intranet apps during large-range recon. It is a 2016 Python 2 tool, unmaintained upstream; the value today is the pattern (nmap XML in, screenshots out), and it runs only where its python2/qt4 dependencies still install.

## Typical use cases

### Scan a range and screenshot every web service

```
autobrowser scan "198.51.100.0/24" -a "-sT -sV -T3" -p project1 --max-workers 10
```

Outcome: nmap runs with the given arguments (`-a`/`--nmap-args`, default `-sS -sV`), each discovered http/https service is then fetched concurrently (10 browser workers) and the screenshots plus JSON land in the `project1` folder. Values with spaces must be double-quoted.

### Scan a host list from a file

```
autobrowser scan hosts.txt -a "-sT -sV -T3" -p project1 --verbose --java-enabled
```

Outcome: like the range scan, but the targets come one per line from `hosts.txt`; `--java-enabled` renders pages with the Java environment enabled (useful for legacy intranet applets), and `--verbose` prints every per-service check as it happens.

### Analyze an existing nmap XML report

```
autobrowser analyze nmap_scan.xml --project report1
```

Outcome: skips the scanning phase entirely — the already-produced nmap XML (from any `nmap -oX` run, including tests done with other tooling) is parsed for http/https services and each one is browsed and screenshotted into `report1`. The cheapest way to add visual evidence to a finished recon pass.

### Route the browsing through a proxy

```
autobrowser analyze nmap_scan.xml --project report1 --proxy socks5://127.0.0.1:8080 --proxy-auth user:Password1
```

Outcome: every page request is relayed through the HTTP/SOCKS5 proxy with the given credentials — point it at an intercepting proxy to capture the traffic each screenshot generated, or reach targets only visible from inside a pivot.
