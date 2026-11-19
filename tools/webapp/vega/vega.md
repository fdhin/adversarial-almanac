---
name: "vega"
categories:
  - webapp
version: "1.0.devel.130"
homepage: "https://github.com/subgraph/Vega"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: other
license: GPL2
meta-checked: "2026-11-09"
github-repo: "subgraph/Vega"
github-stars: 362
github-pushed: "2021-02-23"
github-archived: false
github-issues: 180
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vega

> Subgraph's GUI Java platform for testing web-application security: an automated crawler/scanner plus an intercepting proxy, extensible in JavaScript.

## What it's for

Vega (Subgraph) is an open-source, Java/Eclipse-based GUI platform for testing the security of web applications, running on Linux, OS X and Windows. It operates in two modes: an automated scanner that crawls a website — extracting links, processing forms, and running JavaScript modules at each discovered injection point (XSS, SQL injection, header and TLS/SSL configuration issues and more) — and an intercepting proxy that sits between your browser and the target, showing every request/response, with breakpoints to hold and edit traffic before it passes; HTTPS is handled with dynamically generated certificates. Findings surface as alerts in the scanner UI, scan depth is configurable in preferences, and the module library is user-extensible in JavaScript. Development ended years ago (the last builds are from the mid-2010s); it is a desktop GUI application with no scriptable command-line mode — the packaged vega command only launches the GUI.

## Typical use cases

### Launch the scanner and start a scan

Open the workbench.

```
vega
```

Outcome: opens the Vega GUI in the scanner perspective. Start an automated scan with the new-scan icon or Scan → Start new Scan; the wizard takes a base URI (or a multi-URI target scope) and module selection, after which the crawler runs and alerts accumulate in the scan view. Switch to the proxy perspective and point a browser at Vega's local listener to intercept, inspect and edit traffic with breakpoints instead.

No corpus evidence — derived from upstream documentation (subgraph/Vega wiki: About, Vega-Scanner, Vega-Proxy; ArchStrike packaging).
