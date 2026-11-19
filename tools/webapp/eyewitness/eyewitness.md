---
name: "eyewitness"
categories:
  - blackarch-webapp
version: "v20230525.1.r207.gc3286fb"
homepage: "https://github.com/RedSiege/EyeWitness"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2026-01-30"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "RedSiege/EyeWitness"
github-stars: 5888
github-pushed: "2026-01-05"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# eyewitness

> Screenshots websites, records server header info, and flags known default credentials.

## What it's for

Triages large numbers of web services fast: give it URLs (plain text, Nmap XML or Nessus XML) and it renders each page in a headless Chromium browser, captures the screenshot plus title and header summary, and notes recognized services with known default credentials. The typical workflow is right after a port scan — screenshot every web-facing service and page through the report to decide what deserves manual attention. It does not test for vulnerabilities. Originally written by Chris Truncer; the project is now maintained under the RedSiege organization (the former GitHub locations redirect there).

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Screenshot a single site

Check one target quickly.

```
eyewitness --single https://target.example.com
```

Outcome: the headless browser renders the page and a report (HTML page plus screenshot images) is written to the output directory, showing the rendered site and its server header summary.

### Screenshot a batch of URLs

Work through a recon URL list.

```
eyewitness -f urls.txt -d /tmp/ew-results
```

Outcome: each URL in the file is visited in turn; `-d` sets the output directory, which ends up containing an HTML report with thumbnails, page titles and headers for every target.

### Work directly from Nmap results

Skip building URL lists by hand.

```
eyewitness -x nmap-scan.xml
```

Outcome: web-capable hosts and ports are extracted from the Nmap XML output and screenshotted automatically — Nessus XML files are accepted the same way.

### Tune throughput and pacing

Adapt to slow or rate-limiting targets.

```
eyewitness -f urls.txt --threads 5 --timeout 30 --delay 2 --jitter 5
```

Outcome: the scan runs with 5 concurrent workers, a 30-second page timeout, and a 2-second delay randomized by up to 5 seconds between requests; `--proxy-ip`/`--proxy-port` route the traffic through a proxy when needed.
