---
name: "ratproxy"
categories:
  - blackarch-fuzzer
version: "1.58"
homepage: "http://code.google.com/p/ratproxy/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: make
license: APACHE
meta-checked: "2026-11-09"
github-repo: "wallin/ratproxy"
github-stars: 15
github-pushed: "2015-08-13"
github-archived: false
github-issues: 23
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ratproxy

> Passive web application security audit proxy by Michal Zalewski: browse a site through it and it annotates likely flaws.

## What it's for

ratproxy (Michal Zalewski / lcamtuf, Google, Apache License 2.0) is a semi-automated, largely passive web application security audit tool. Instead of attacking a target, you browse it normally with ratproxy configured as your HTTP(S) proxy; the tool observes the real user-driven traffic and annotates security-relevant patterns: cross-domain trust leaks (token leakage via Referer, untrusted script inclusion, mixed content), bad caching headers on sensitive responses, MIME and charset mismatches that lead to XSS, directory traversal candidates, weak or missing XSRF defenses, risky JSON serving, and discouraged JavaScript (`eval`, `.innerHTML`). Passive operation makes it safe to run against production systems; an optional `-X` mode adds lightweight, disruptive validation of preselected XSS/XSRF candidates. Output is a machine-readable log that `ratproxy-report.sh` pretty-prints into a prioritized HTML report. It is not a crawler or active scanner — coverage comes entirely from genuine manual browsing through the proxy, and SSL interception uses a self-signed certificate the browser must accept.

## Typical use cases

### Run a passive audit

Collect findings while exercising the application by hand.

```
ratproxy -v traces -w ratproxy.log -d example.com -lfscm
```

Outcome: ratproxy listens on 127.0.0.1:8080 — point the browser's proxy settings at it, log into and thoroughly exercise the target, then Ctrl-C. The raw log lands in `ratproxy.log` with full request/response traces under `traces/`. `-d example.com` defines the audited perimeter (cross-domain checks do nothing without it), and `-lfscm` enables the recommended check set: relaxed page comparison (`-l`), Flash logging (`-f`), POST request logging (`-s`), cookie-setter logging (`-c`), and cross-domain active content (`-m`).

### Add active validation of XSS/XSRF defenses

Let ratproxy confirm high-likelihood candidates itself.

```
ratproxy -v traces -w ratproxy.log -d example.com -lfscm -XC
```

Outcome: in addition to passive collection, ratproxy replays carefully chosen requests with tweaked parameters to validate XSS filtering and XSRF tokens on preselected candidates; `-C` replays each original unmodified request afterwards to restore server-side state. Disruptive — use only against systems where that is acceptable.

### Generate the HTML report

Turn the raw log into something triageable.

```
ratproxy-report.sh ratproxy.log > report.html
```

Outcome: the pretty-printer dedupes log entries, groups them by issue class, and sorts by priority into a single HTML page with request-replay links; open `report.html` in a browser to review findings (on BlackArch the script ships at `/usr/share/ratproxy/ratproxy-report.sh`).

### Audit an HTTPS-only application on a custom port

Adapt the listener to the engagement.

```
ratproxy -v traces -w ratproxy.log -d example.com -lfscm -k -p 8081
```

Outcome: `-k` treats the target as HTTPS-only and flags any downgrade to HTTP as a finding; `-p 8081` moves the proxy listener off the default port 8080. Browse as usual; accept the proxy's self-signed certificate when visiting SSL pages.

*No corpus evidence — derived from upstream documentation.*
