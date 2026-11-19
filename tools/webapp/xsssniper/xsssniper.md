---
name: "xsssniper"
categories:
  - blackarch-webapp
version: "79.02b59af"
homepage: "https://github.com/gbrindisi/xsssniper"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "gbrindisi/xsssniper"
github-stars: 420
github-pushed: "2018-04-17"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xsssniper

> Handy single-purpose XSS discovery tool with crawling, form discovery, POST testing and DOM heuristics.

## What it's for

xsssniper (by Gianluca Brindisi) is a small XSS discovery tool: it takes a target URL and tests its parameters for reflected cross-site scripting, with optional mass scanning — crawling the site for further links to test, hunting for forms, and a basic heuristic to detect DOM XSS sinks and sources in the page's JavaScript. It supports POST data, cookies, custom or random user agents, HTTP proxying and routing the scan through Tor. It deliberately does one job with a handful of options; there is no payload browser, WAF evasion or reporting pipeline. The distro package installs the script as the `xsssniper` command.

## Typical use cases

### Test a GET parameter

```
xsssniper -u "http://target.example.com/index.php?page=test"
```

Outcome: every parameter of the URL is tested for reflection and the tool reports which ones are injectable.

### Test POST parameters

```
xsssniper -u "http://target.example.com/index.php" --post --data="page=test&lang=en"
```

Outcome: the supplied body is submitted via POST and its fields are tested instead of the query string.

### Discover and test forms

```
xsssniper -u "http://target.example.com" --forms
```

Outcome: the target page is parsed for forms, which are filled and submitted so each field becomes a tested injection point.

### Mass-scan a whole site

```
xsssniper -u "http://target.example.com" --crawl --forms
```

Outcome: xsssniper crawls the target for other links, testing every URL and every form found along the way — the site-wide variant of the single-URL check.

### Hunt DOM XSS sinks and scan via Tor

```
xsssniper -u "http://target.example.com" --dom --tor --random-agent
```

Outcome: embedded and linked JavaScript is analyzed for common sources/sinks (DOM XSS heuristic), with all scan traffic routed through Tor under rotating user agents.

*No corpus evidence — derived from upstream documentation (README usage section of gbrindisi/xsssniper).*
