---
name: "webslayer"
categories:
  - blackarch-webapp
version: "5"
homepage: "https://code.google.com/archive/p/webslayer/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: LGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Malphaet/webslayer"
github-stars: 7
github-pushed: "2012-10-02"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webslayer

> GUI workbench for brute-forcing web applications: login forms, sessions, parameters, and hidden resource discovery.

## What it's for

WebSlayer (an OWASP project by Christian Martorella) is a PyQt-based brute-forcing tool for web penetration tests. You paste a raw HTTP request as a template, mark the payload positions, and replay the request with dictionaries to brute-force logins, session tokens or parameters, or to discover hidden files and directories (predictable resource locator with recursion, common extensions, and non-standard response-code detection). It supports Basic and NTLM authentication, around 15 payload encodings, two simultaneous payloads in different request parts, authenticated and randomized proxy balancing, live result filters, and session saving. It is a purely graphical, Google Code-era Python 2/PyQt4 application with no CLI attack mode, unmaintained since 2012.

## Typical use cases

### Launch the brute-forcing GUI

Start WebSlayer's graphical console.

```
webslayer
```

Outcome: opens the PyQt4 window where a request template is loaded, payload positions and encodings are chosen, dictionaries are selected, and an attack is started and monitored; results can be filtered live and the session saved.

*No corpus evidence — derived from upstream documentation (OWASP WebSlayer README; the GitHub repo linked in the frontmatter is a mirror of the archived Google Code source tree).*
