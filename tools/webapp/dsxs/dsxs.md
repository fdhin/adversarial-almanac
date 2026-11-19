---
name: "dsxs"
categories:
  - blackarch-webapp
version: "135.a7b8f0e"
homepage: "https://github.com/stamparm/DSXS"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "stamparm/DSXS"
github-stars: 428
github-pushed: "2026-10-26"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 6
gh-activity-checked: "2026-11-09"
---

# dsxs

> A fully functional Cross-site scripting vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

## What it's for

DSXS (Damn Small XSS Scanner) is Miroslav Stampar's cross-site scripting scanner in under 100 lines of Python: it tests each GET (or POST, via `--data`) parameter of a URL for reflected XSS — injecting probe payloads and checking whether they land in the response unfiltered and outside of tags — and additionally flags pages whose own DOM script sinks make them vulnerable without any parameter. Reach for it as a fast, dependency-free, auditable XSS triage pass; it identifies candidate reflections, it does not craft weaponized exploits or crawl a site for you. Proxy and `Cookie`/`User-Agent`/`Referer` headers cover authenticated or proxied targets. The BlackArch package installs the script as `/usr/bin/dsxs`.

## Typical use cases

### Test a URL's parameters for reflected XSS

The canonical run with a candidate parameter.

```
dsxs -u "http://target.example.com/search.php?test=query"
```

Outcome: probes each GET parameter with XSS payloads and prints findings such as `GET parameter 'test' appears to be XSS vulnerable (">.xss.<", outside of tags, no filtering)`, ending with `scan results: possible vulnerabilities found`; a page flagged `page itself appears to be XSS vulnerable (DOM)` indicates a sink in the page's own script with no usable parameters.

### Test GET and POST parameters together

Cover a form field in the same run.

```
dsxs -u "http://target.example.com/search.php?test=query" --data="searchFor=foobar"
```

Outcome: scans both the GET and the POST parameter (`* scanning GET parameter 'test'` / `* scanning POST parameter 'searchFor'`) and reports whichever reflections survive, with the parameter and context named.

### Scan through a proxy with a session

Inspect or authenticate the scan traffic.

```
dsxs -u "http://target.example.com/profile.php?name=user" --proxy "http://127.0.0.1:8080" --cookie "session=abc123"
```

Outcome: sends every probe through the intercepting proxy with the cookie applied, so authenticated pages can be tested and each payload's reflection reviewed in Burp.

No corpus evidence — derived from upstream documentation (README sample runs and options).
