---
name: "aemscan"
categories:
  - windows
homepage: "https://github.com/Raz0r/aemscan"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: python
license: MIT
meta-checked: "2026-11-13"
github-repo: "Raz0r/aemscan"
github-match: "exact"
github-stars: 184
github-pushed: "2023-05-22"
github-issues: 5
gh-checked: "2026-11-13"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# aemscan

> Adobe Experience Manager vulnerability scanner: default-credential bruteforce, error-page info leaks, WebDav exposure, version detection and useful-path probing in one command.

## What it's for

aemscan (by Raz0r) is a compact single-purpose scanner for Adobe Experience
Manager instances. Point it at a URL and it checks the classic AEM exposure
set: default credentials (admin:admin and friends) by bruteforce,
information leak through the default error page, WebDav support (the
WebDav/OSGi XXE of CVE-2015-1833), AEM version detection and a scanner for
known-useful paths. It predates the SSRF-era CVEs — those checks are on the
author's TODO (CVE-2016-0956, CVE-2018-5006/12809) — so pair it with
[[aem-hacker]] when you need the newer servlet/SSRF checks; aemscan's value
is its fast, zero-configuration first pass.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### First-pass scan of an AEM instance

Run the whole check set against one target.

```
aemscan https://aem.example.com
```

Outcome: version detection runs first, then the default-credential
bruteforce, error-page information leak, WebDav support check (CVE-2015-1833
context) and useful-paths probing — findings print to the terminal as each
check completes. No flags exist; the URL is the only argument.
