---
name: "webxploiter"
categories:
  - blackarch-webapp
version: "56.c03fe6b"
homepage: "https://github.com/xionsec/WebXploiter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "xionsec/WebXploiter"
github-stars: 93
github-pushed: "2016-02-29"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webxploiter

> Single-target recon plus OWASP Top 10 oriented checks: injection, XSS, and known-software weaknesses.

## What it's for

WebXploiter (by Anirudh Anand, OWASP) is a Python recon-and-check tool for one target URL. It profiles HTTP response headers, robots.txt, and cookies (automatically decoding base64 values), checks enabled HTTP methods and information-disclosure artifacts (.htaccess and .svn exposure, IIS internal-IP disclosure), then probes for error-based SQL injection (cookie and User-Agent vectors), CRLF and Host-header injection, clickjacking exposure, and known software vulnerabilities (WordPress, Apache). Findings are printed with proof-of-concept strings and reference links. It is a lightweight single-endpoint checker, not a crawler or full scanner.

## Typical use cases

### Run every check against a target

```
webxploiter -u http://target.example.com -a
```

Outcome: prints response headers, robots.txt analysis, cookie analysis with base64 decoding, then any injection, host-header, clickjacking, and known-software findings with POC strings.

### Test only injection attacks

```
webxploiter -u http://target.example.com/action.php -A1
```

Outcome: runs the injection checks only — error-based SQLi via cookies and User-Agent, CRLF injection, and Host-header injection.

### Test only XSS

```
webxploiter -u http://target.example.com -A3
```

Outcome: runs only the cross-site scripting checks.

### Check for known software vulnerabilities

```
webxploiter -u http://target.example.com -A9
```

Outcome: runs only the known-vulnerability checks for the software detected on the target (WordPress, Apache); a dedicated `-wordpress` flag narrows this to WordPress checks.

*No corpus evidence — derived from upstream documentation; flags verified against the packaged source's argparse, which defines more modules than the older README.*
