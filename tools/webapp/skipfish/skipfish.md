---
name: "skipfish"
categories:
  - blackarch-webapp
version: "2.10b"
homepage: "http://code.google.com/p/skipfish/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-11-10"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "spinkham/skipfish"
github-stars: 883
github-pushed: "2023-02-18"
github-archived: false
github-issues: 6
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skipfish

> Google's classic active web application security scanner — fast, recursive, dictionary-driven crawl and probe (final release 2.10b).

## What it's for

skipfish is Google's C-written, fully asynchronous web application security recon tool (by
Michal Zalewski, last upstream release 2.10b circa 2012; the project is unmaintained but still
packaged). It crawls a target adaptively, builds its own wordlist from site content, and probes
for injection flaws (blind SQL/shell/XML/XPath), XSS, directory traversal, MIME/charset and
caching issues, plus PUT-enabled locations — favoring differential three-step probes over
signature matching to keep false positives low. Results land in an interactive HTML report with
the underlying data as JSON. It has no CVE database for banner checks and is not built for
hostile/misbehaving servers; treat it as a thorough but dated first-pass scanner.

## Typical use cases

### Scan a site with dictionary learning

```
skipfish -o outdir -S /usr/share/skipfish/dictionaries/complete.wl -W learned.wl http://target.example.com/
```

Outcome: the target is crawled and probed; newly learned site-specific keywords accumulate in
`learned.wl` for sharper re-runs, and the final report is written to `outdir/index.html` (view
in a browser; the raw findings are JSON files plus a `pivots.txt` list of discovered URLs).

### Keep the scan on scope

```
skipfish -o outdir -D .example.com -X /icons/ -K jsessionid http://target.example.com/
```

Outcome: `-D` widens the in-scope domain set (`.example.com` covers subdomains), `-X` excludes
noise locations from being fetched, and `-K` stops the named parameter (a URL session id) from
being fuzzed — together they keep the crawl inside the application and out of irrelevant
territory.

### Scan from a list of seed URLs

```
skipfish -o outdir @seed_urls.txt
```

Outcome: every URL listed in the file is treated as a crawl root (multiple starting points,
multi-port entry URLs), useful when the interesting surface is not reachable from the homepage.

### Mimic a real browser and add headers

```
skipfish -o outdir -b ffox -H "X-Forwarded-For: 198.51.100.10" http://target.example.com/
```

Outcome: requests are sent with a Firefox-shaped header set plus the custom header, getting past
sites that reject the default minimal headers (`-b ie` and `-b phone` are the other personas).

No corpus evidence — derived from upstream documentation (README, Google Code era).
