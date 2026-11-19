---
name: "paros"
categories:
  - blackarch-webapp
version: "3.2.13"
homepage: "http://www.parosproxy.org"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "trol73/paros"
github-stars: 11
github-pushed: "2014-02-21"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# paros

> Classic Java intercepting proxy (2006) with spider and XSS/SQLi scanning — the direct ancestor of OWASP ZAP.

## What it's for

Paros is a Java-based HTTP/HTTPS proxy for assessing web application vulnerabilities: it traps and edits HTTP messages on the fly, spiders the target site, supports client certificates and proxy chaining, and includes a scanner that probes for XSS and SQL injection. Everything happens in its Swing GUI, backed by a built-in HSQLDB session database. The project ended around 2006 and its website is gone; the codebase lives on as OWASP ZAP, which is the maintained choice today. Paros remains useful mainly as a small, self-contained proxy for studying legacy tooling or old environments.

## Typical use cases

### Start the proxy

```
paros
```

Outcome: launches the GUI (a wrapper for `java -jar /usr/share/paros/paros.jar`) with the intercepting proxy listening on 127.0.0.1:8080 — point a browser at that address and traffic appears in Paros' Sites and History panels, where requests can be trapped, edited, and re-sent, and the spider and scanner started against the recorded session.

No corpus evidence — derived from upstream documentation (release archive + package recipe).
