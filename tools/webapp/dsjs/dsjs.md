---
name: "dsjs"
categories:
  - blackarch-webapp
version: "33.e8af886"
homepage: "https://github.com/stamparm/DSJS"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-02"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "stamparm/DSJS"
github-stars: 109
github-pushed: "2026-07-20"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# dsjs

> A fully functional JavaScript library vulnerability scanner written in under 100 lines of code.

## What it's for

DSJS (Damn Small JS Scanner) is Miroslav Stampar's JavaScript-library vulnerability checker in under 100 lines of Python — a derivative of the Retire.js project. Give it a page URL and it fetches the page, identifies the JavaScript libraries it loads (jQuery, AngularJS, Backbone, Dojo, Ember, Handlebars, Plupload, Prototype, YUI and more) with their versions, and reports libraries with known vulnerabilities along with the advisory links. Reach for it as a one-shot known-vulnerable-JS-library sweep of a page; it does not audit custom application JavaScript, only the known library set of its bundled (Retire.js-derived) rules. Proxy and `Cookie`/`User-Agent`/`Referer` headers are supported. The BlackArch package installs the script as `/usr/bin/dsjs`.

## Typical use cases

### Check a page for vulnerable JavaScript libraries

The canonical single-URL run.

```
dsjs -u "http://target.example.com"
```

Outcome: fetches the page, fingerprints every recognized JavaScript library and version, and prints a line per vulnerable library such as `jquery v1.9.1 (< v3.4.0)` with the advisory/CVE links, ending with `scan results: possible vulnerabilities found` — the list to verify and report.

### Scan an authenticated page

Reach pages behind a login.

```
dsjs -u "http://target.example.com/dashboard" --cookie "session=abc123" --proxy "http://127.0.0.1:8080"
```

Outcome: performs the same library fingerprinting with the session cookie applied and every request visible in the proxy, so internal, authenticated pages get the same vulnerable-library sweep.

No corpus evidence — derived from upstream documentation (README sample runs and options).
