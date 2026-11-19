---
name: "xsrfprobe"
categories:
  - blackarch-webapp
version: "590.0dbc5c2"
homepage: "https://github.com/0xInfection/XSRFProbe"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-19"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0xInfection/XSRFProbe"
github-stars: 1308
github-pushed: "2026-07-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 12
gh-activity-checked: "2026-11-09"
---

# xsrfprobe

> Cross-Site Request Forgery audit and exploitation toolkit with a crawling engine and systematic token checks.

## What it's for

XSRFProbe audits web applications for CSRF weaknesses with a battery of systematic, identifier-tagged checks: missing anti-CSRF tokens (including login CSRF), token tampering (omission, empty or duplicated values, method-switch validation, tokens not bound to the session, naive double-submit cookies, custom-header tokens), Referer/Origin validation bypasses (header removal, regex and subdomain tricks, `Origin: null`), method-override and Content-Type bypasses, SameSite cookie posture, and token-strength analysis via entropy checks. A response-diffing/benchmark engine backs the HTTP-level checks before anything is declared vulnerable, and each finding can generate a working — normal or malicious — proof of concept. It actively submits forms against the target, so upstream explicitly warns to run it only against disposable test setups, never live sites.

## Typical use cases

### Audit a single endpoint

Scan one known form-bearing page.

```
xsrfprobe -u http://target.example.com/testcsrf.php
```

Outcome: XSRFProbe fetches and parses the endpoint's forms, runs its token/referer/origin checks, prints any findings tagged with their check IDs (e.g. `[T6] VULNERABLE`), and writes logs plus generated PoCs under `xsrfprobe-output/` in the working directory.

### Crawl and audit a whole site

Let the built-in same-origin crawler discover and test every endpoint.

```
xsrfprobe -u http://target.example.com --crawl
```

Outcome: the crawler walks the site (normalizing paths, skipping logout/tracking/off-site links) and audits each discovered form; expect a high request volume against the target, which is why this belongs on lab systems only.

### Bound the crawl on a large site

Cap URLs, depth, and wall-clock time so a crawl cannot run unbounded.

```
xsrfprobe -u http://target.example.com --crawl --max-urls 500 --max-depth 3 --crawl-timeout 120
```

Outcome: the crawl stops at 500 URLs, 3 link levels from the seed, or 120 seconds — whichever comes first — then auditing of the collected endpoints proceeds and results land in the output folder.

### Test an authenticated session

Supply the session cookies every request should carry.

```
xsrfprobe -u http://target.example.com/account.php -c "SESSION_ID=abcdefgh123,_gid=xyz789"
```

Outcome: all requests, form parses, and PoC generation run as the logged-in user, which is required to reach authenticated forms where token-binding flaws (T4/T5) actually show.

### Emit a JSON report

Get a machine-readable findings file instead of console-only output.

```
xsrfprobe -u http://target.example.com --crawl --json
```

Outcome: in addition to the console output, `report.json` is written into the output directory, with one entry per finding carrying its `test_id`, severity, and exploitability details — ready for downstream tooling. Browser-dependent SameSite checks can be added with `--browser` (headless Firefox via geckodriver).

No corpus evidence — derived from upstream documentation.
