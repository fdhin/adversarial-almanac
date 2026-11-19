---
name: "sitadel"
categories:
  - blackarch-webapp
version: "199.077c4e4"
homepage: "https://github.com/shenril/Sitadel"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "shenril/Sitadel"
github-stars: 611
github-pushed: "2026-10-15"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 76
gh-activity-checked: "2026-11-09"
---

# sitadel

> Modular web application security scanner: fingerprinting plus plugin-based attack checks (Python 3.11+ WASan successor).

## What it's for

Sitadel is a modernized descendant of WASan: a web application scanner that splits work into
fingerprint modules (server, framework, frontend framework, CMS, WAF, language, OS, CDN, header
audit, cookie security) and attack modules (bruteforce of admin panels/backups/directories;
SQL/HTML/LDAP/XPath/XSS/RFI/SSTI/LFI injections; known vulns like Shellshock and CRIME; probes
for WebDAV, robots, phpinfo, CORS, open redirect, SSRF and more). Risk levels gate which attacks
run, modules can be cherry-picked, and new checks drop in through its plugin system. Expect
noisy generic findings on complex apps — it complements, not replaces, manual testing.

## Typical use cases

### Full scan of a target

```
sitadel http://target.example.com
```

Outcome: fingerprinting runs first (server, language, CMS, WAF...), then the enabled attack
modules fire; findings are reported per module to the console.

### Aggressive scan without following redirects

Raise the risk level and keep the tested URL stable.

```
sitadel http://target.example.com -r 2 --no-redirect
```

Outcome: risk level 2 enables attacks skipped at lower levels, and 302 responses are not
followed — useful when a redirect would drag the scanner off the application under test.

### Run only chosen modules, verbosely

```
sitadel http://target.example.com -a bruteforce -f header server -v
```

Outcome: only the bruteforce attack family plus the header and server fingerprints execute, with
increased log detail (`-vv`, `-vvv` for more); this is the fast way to answer one specific
question instead of running the whole battery.

### Scan through a proxy with a session cookie

```
sitadel http://target.example.com -c "session=Password1" -p http://127.0.0.1:8080
```

Outcome: requests carry the supplied cookie and are routed through the proxy (e.g. an
intercepting proxy), so authenticated or proxied targets can be inspected while every request is
observable.

No corpus evidence — derived from upstream documentation (README).
