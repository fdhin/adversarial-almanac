---
name: "plown"
categories:
  - blackarch-webapp
version: "13.ccf998c"
homepage: "https://github.com/unweb/plown"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "unweb/plown"
github-stars: 19
github-pushed: "2012-05-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# plown

> Plone CMS security scanner: username/vulnerability enumeration plus threaded password brute-forcing.

## What it's for

Checking Plone sites for the two things Plone's own hardening usually leaves open: weak passwords and unpatched hotfixes. It has two modes — enumeration, which harvests usernames and probes a small set of known Plone vulnerabilities/hotfix gaps (e.g. the 20110622 and 20110928 hotfixes, CVE-2006-4247), and brute force, which throws a users×passwords list at the login form with multiple threads (16 by default). Written in 2012-era Python 2 and its vulnerability dictionary is frozen at that era, so treat it as a password-audit and historical-check helper for legacy Plone, not a current Plone vulnerability scanner. Bundles `userslist`/`passlist` sample files in the repo; the BlackArch package wraps `plown.py` as the `plown` command.

## Typical use cases

### Enumerate users and known vulnerabilities

Run the default enumeration mode against a Plone instance.

```
plown http://target.example.com:8080/Plone
```

Outcome: discovers valid usernames and reports which of the built-in known Plone vulnerabilities/hotfixes the instance appears to be missing; run without arguments to see the full help.

### Brute-force credentials with custom lists

Feed it user and password dictionaries in threading mode.

```
plown -b -U userslist -P passlist http://target.example.com
```

Outcome: attempts every user/password pair against the site's login form using 16 parallel threads (`userslist`/`passlist` from the repo are the sample lists) and reports successful authentications.

### Tune thread count and login form location

Speed up audits or point at a non-standard login form.

```
plown -b -U userslist -P passlist -T 30 -l https://target.example.com/login_form http://target.example.com
```

Outcome: brute-forces with 30 threads instead of the default 16, authenticating against the explicit login-form URL given with `-l` — useful when the Plone login lives at a custom path.

*Provenance: no corpus evidence — derived from upstream documentation (README + `plown.py` optparse usage, verified 2026-11).*
