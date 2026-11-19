---
name: "strutscan"
categories:
  - blackarch-scanner
version: "4.8712c12"
homepage: "https://github.com/riusksk/StrutScan"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "riusksk/StrutScan"
github-stars: 23
github-pushed: "2016-02-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# strutscan

> Perl auditor for legacy Apache Struts2 OGNL code-execution vulnerabilities.

## What it's for

StrutScan (riusksk) is a 2014-era Perl script that audits Apache Struts2
endpoints for the classic OGNL expression-injection family of remote-code
execution bugs: crafted parameter payloads that reach
`@java.lang.Runtime@getRuntime().exec()`, file-read payloads, and the Struts
developer-mode `debug=command&expression=` exploit. Give it a single
`.action` URL, or a Google query and it batch-discovers
`.action`/`.do`/`.xhtml` endpoints from search results and audits each one,
reporting which payloads produced output. Its console strings are Chinese.
It targets long-patched Struts2 versions and its Google-scraping mode is
era-typical, so treat it as a tool for deliberately vulnerable lab targets
rather than modern applications.

## Typical use cases

### Audit a known Struts2 endpoint

Point it at one URL (upstream `perl StrutScan.pl`, installed as `strutscan`).

```
strutscan -u http://target.example.com/index.action
```

Outcome: the script appends its OGNL and DevMode payloads to the URL and
prints any that return command output (for example a readable /etc/passwd),
finishing with a count of successful payloads.

### Batch-audit endpoints found via search

Let the tool gather targets with a Google dork.

```
strutscan -g "site:example.com filetype:action" -p 1
```

Outcome: Google is queried for matching URLs (100 results per page, `-p`
selecting the starting page), and every unique endpoint found is then audited
with the same payload set.

*No corpus evidence — derived from the packaged upstream source
(riusksk/StrutScan README and the usage() block in StrutScan.pl).*
