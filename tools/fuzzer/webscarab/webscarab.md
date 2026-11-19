---
name: "webscarab"
categories:
  - blackarch-fuzzer
version: "20120422.001828"
homepage: "http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project"
packaged-by: [blackarch, kali]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "OWASP/OWASP-WebScarab"
github-stars: 613
github-pushed: "2021-08-13"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webscarab

> OWASP's veteran Java proxy framework for intercepting and analysing HTTP(S) traffic.

## What it's for

WebScarab (Rogan Dawes) is an intercepting web proxy wrapped in an analysis framework: it sits between browser and server, records every request and response, and adds tools for manual review — request/response editing and replay, session ID analysis, site mapping and spidering, parameter fuzzing, and SOAP-specific tooling. It predates Burp Suite and ZAP and filled the same manual-testing role; the "Lite" mode is a simplified single-window variant of the full multi-plugin Swing UI. Its last release dates from 2012 — the project's GitHub repo (OWASP/OWASP-WebScarab) has seen only light maintenance commits since — and it is distributed as a self-contained Java jar from the author's site, with no scriptable command-line interface — it is driven entirely through its GUI (Proxy, Summary, SessionID, Spider, Fuzzer panels), so modern automation needs belong to its successors rather than WebScarab.

WebScarab ships no command-line surface, so this note deliberately provides no runnable examples.

*No corpus evidence — derived from upstream documentation.*
