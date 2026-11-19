---
name: "vulnerabilities-spider"
categories:
  - blackarch-webapp
version: "1.426e70f"
homepage: "https://github.com/muhammad-bouabid/Vulnerabilities-spider"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "muhammad-bouabid/Vulnerabilities-spider"
github-stars: 2
github-pushed: "2016-07-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vulnerabilities-spider

> GTK desktop GUI that drives a Selenium browser over a target URL and flags injection points (SQLi, XSS, LFI, XPath, RCE) with OWASP references.

## What it's for

Vulnerabilities-spider (Mohammad Bouabid, 2016) is a small Python GTK desktop application for web-vulnerability scanning: you enter a target URL in its dialog and the tool drives a Selenium-controlled Firefox over the site, testing pages with payloads for SQL injection, XSS, local file inclusion, XPath injection and remote command execution, then reporting each vulnerable "point" it finds together with an OWASP reference for follow-up reading. All interaction happens in the GUI — the packaged command takes no arguments and there is no CLI surface. It is a Python 2 era project (old Selenium/GTK bindings, BeautifulSoup), unmaintained since 2016; treat it as a historical GUI experiment rather than a dependable scanner.

## Typical use cases

### Launch the GUI and scan a URL

Open the spider and point it at a target.

```
vulnerabilities-spider
```

Outcome: opens the GTK window; enter the target URL in the Entry Url field, choose to run the scan directly or after configuring the scanning process, and the spider reports detected injection points (SQL, XSS, LFI, XPath, RCE) with their OWASP links in the results pane.

No corpus evidence — derived from the packaged upstream source (VulnerabilitiesSpider.py GTK/Selenium flow of muhammad-bouabid/Vulnerabilities-spider).
