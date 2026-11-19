---
name: "conscan"
categories:
  - blackarch-fuzzer
version: "1.2"
homepage: "http://nullsecurity.net/tools/scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "hadoocn/conscan"
github-stars: 2
github-pushed: "2014-07-26"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# conscan

> A blackbox vulnerability scanner for the concrete5 CMS by TheXero (NullSecurity).

## What it's for

conscan audits a concrete5 CMS installation purely from the outside. It
detects the CMS and its version, matches that against a bundled XML database
of known concrete5 vulnerabilities (`data/cmsvulns.xml`), looks for
full-path-disclosure issues, can enumerate CMS users, and can brute-force one
account's login credentials from a wordlist. It speaks only HTTP(S) to the
target — the detection and enumeration passes need no credentials or server
access — which makes it a quick first-pass check for a concrete5 site whose
internals you cannot inspect. It is a Python 2 script and its vulnerability
database reflects the tool's era, so treat version matches as leads to verify,
not as a current CVE feed.

## Typical use cases

### Detect concrete5 and check known vulnerabilities

Point it at a site and let the fingerprint, version and vulnerability pass
run.

```
conscan -t http://target.example.com/concrete/
```

Outcome: prints the banner, then reports whether concrete5 was detected, the
identified version, and any matching entries from the vulnerability database,
including full-path-disclosure findings; a path after the host is accepted to
scan installs not rooted at `/`.

### Enumerate CMS users

Add enumeration to the basic scan.

```
conscan -t http://target.example.com/concrete/ -e
```

Outcome: after the detection pass, the enumeration module runs and lists user
accounts discovered from the concrete5 installation.

### Brute-force one account

Supply a username together with a wordlist (both are required).

```
conscan -t http://target.example.com/concrete/ -u user -p wordlist.txt
```

Outcome: after the detection pass, the login module tries every wordlist
entry against the named account over the site's login form and reports any
working credentials; giving `-u` without `-p` exits with an error asking for
the wordlist.

### Refresh the vulnerability database

```
conscan --update
```

Outcome: downloads the current vulnerability data into the local `data/`
directory, prints `Update successful!` and exits without touching the
target.

*No corpus evidence — derived from upstream documentation.*
