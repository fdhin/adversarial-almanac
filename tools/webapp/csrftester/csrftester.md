---
name: "csrftester"
categories:
  - blackarch-webapp
version: "1.0"
homepage: "http://www.owasp.org/index.php/Category:OWASP_CSRFTester_Project"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: LGPL
meta-checked: "2026-11-09"
github-repo: "ot-jerry-welch/owaspcsrftester"
github-stars: 4
github-pushed: "2016-02-09"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# csrftester

> OWASP CSRFTester — Java GUI that records requests through a local proxy and replays them without their anti-CSRF token.

## What it's for

CSRFTester tests whether an application's state-changing requests actually depend on their anti-CSRF token. It is a Swing GUI wrapping an embedded intercepting proxy (based on OWASP WebScarab classes): browse the target through the proxy, pick a recorded request, strip the token parameter, and generate an HTML proof-of-concept page — form, frame, image, link or XHR variant — that replays the request. If the action still succeeds when that page is loaded in the tester's browser, the request is CSRF-able. It finds and demonstrates the flaw; for hosting multi-step exploits see [[csrft-git]].

GitHub: third-party mirror of the official OWASP CSRFTester Google Code project (owaspcsrftester.googlecode.com).

## Typical use cases

### Launch the tester and record traffic

```
csrftester
```

Outcome: opens the CSRFTester GUI and starts its local proxy on port 8008; point the test browser's proxy at it, walk through the action under test, and each captured request appears in the request list, ready to be stripped of its token parameter and exported as a PoC page via the report generators.

_No corpus evidence — derived from the packaged upstream release (launcher from the packaging recipe; default proxy port and the form/frame/img/link/XHR report generators verified from the shipped jar's classes)._
