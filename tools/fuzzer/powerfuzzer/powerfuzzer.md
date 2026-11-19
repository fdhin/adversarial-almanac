---
name: "powerfuzzer"
categories:
  - blackarch-fuzzer
version: "1_beta"
homepage: "http://www.powerfuzzer.com"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "foreni-packages/powerfuzzer"
github-stars: 4
github-pushed: "2015-09-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# powerfuzzer

> Highly automated GUI web fuzzer that spiders a site, attacks every discovered input, and reports XSS, injection, and error-page findings.

## What it's for

PowerFuzzer (Marcin Kozlowski, 2008) is a highly automated web application fuzzer assembled from ideas and components of other open-source fuzzers (including Wapiti's lswww spider, JBroFuzz, and Socket Fuzzer). It spiders a target website, identifies its inputs, and probes them for cross-site scripting; SQL, LDAP, code, command, and XPATH injection; CRLF issues; and HTTP 500 responses. Everything is configured in a wxWidgets GUI — target URL, HTTP credentials, cookies in LWP format, proxy, timeout, verbosity, and up to four excluded URLs — there are no command-line flags for fuzzing itself. It is Python 2 / wxWidgets era software, so it needs a Python 2 environment to run.

## Typical use cases

### Fuzz a site from the GUI

```
powerfuzzer
```

Outcome: opens the wxWidgets window; enter the target (for example http://target.example.com/), optionally credentials, proxy, and excluded URLs, then start the run — the spider maps the site, every discovered input is fuzzed, and findings accumulate in the built-in report view.

### Capture a session cookie for authenticated scanning

```
python2 /usr/share/powerfuzzer/getcookie.py cookies.txt "http://target.example.com/login?user=user&password=Password1"
```

Outcome: requests the login form URL and dumps the session cookies it receives into cookies.txt in LWP format; load that file through the GUI's Cookie button so the fuzzing run sees the authenticated areas of the site.

*No corpus evidence — derived from upstream documentation.*
