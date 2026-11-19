---
name: "jast"
categories:
  - blackarch-webapp
version: "17.361ecde"
homepage: "https://github.com/mikehacksthings/jast"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom::unknown
meta-checked: "2026-11-09"
github-repo: "mikehacksthings/jast"
github-stars: 10
github-pushed: "2019-07-18"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jast

> Just Another Screenshot Tool: Selenium-based bulk screenshots of web hosts with an HTML report.

## What it's for

Jast automates taking screenshots of web hosts. It drives Firefox through the Selenium webdriver, captures one image per host, and writes everything into an HTML report you can hand to a client or skim yourself after a large recon pass. It handles single URLs or a file of hosts (format `http(s)://<host>:<port>` — anything else is skipped with an error), can follow redirects before capturing, record response headers under each screenshot, and set the browser window size. It is purely a capture-and-report tool: it does no vulnerability checking.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Screenshot a single URL

Capture one host and build its report.

```
jast -u https://www.example.com -o ./report
```

Outcome: Firefox opens headlessly via Selenium, captures the page, and `./report` contains `report.html` plus a `screenshots/` subfolder with the PNG.

### Screenshot a list of hosts

Run the same capture across a recon host file.

```
jast -f hosts.txt -o ./report
```

Outcome: every host in `hosts.txt` (one `http(s)://<host>:<port>` entry per line) is visited in turn; the HTML report ends up with one screenshot card per host, and malformed entries are reported and skipped.

### Capture through redirects with headers and a custom window size

Record what actually renders after redirections, with response headers, at a fixed viewport.

```
jast -u http://www.example.com -o ./report --follow-redirects --headers --size=1024x768
```

Outcome: redirects are followed before the shot, the response headers are written beneath the screenshot in the report, and the browser window is sized 1024x768 instead of the default 800x600.
