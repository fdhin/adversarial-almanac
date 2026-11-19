---
name: "firefox-security-toolkit"
categories:
  - blackarch-misc
version: "17.d356ab8"
homepage: "https://github.com/mazen160/Firefox-Security-Toolkit"
packaged-by: [blackarch]
blackarch-builddate: "2025-05-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mazen160/Firefox-Security-Toolkit"
github-stars: 518
github-pushed: "2025-05-14"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# firefox-security-toolkit

> Turns a stock Firefox install into a web-application testing suite in one run.

## What it's for

An interactive shell script by Mazin Ahmed that prepares a Firefox profile for web-application penetration testing: it downloads and installs the security extensions the community voted most useful (HackBar, FoxyProxy, Wappalyzer, Shodan.io, Easy XSS, CSRF Spotter and more), fetches the Burp Suite CA certificate for intercepting HTTPS traffic, and pulls a large user-agent list for User-Agent Switcher. It installs existing add-ons from the Mozilla store — it is not itself an exploit, proxy or scanner, and it changes nothing outside the local browser profile.

## Typical use cases

### Set up a pentest browser profile

Run the toolkit and pick from its interactive menu.

```
firefox-security-toolkit
```

Outcome: the script offers to download and install the security add-on set plus the Burp Suite certificate and the user-agent list into the local Firefox profile; afterwards the browser is ready for proxied, extension-assisted web testing.

*No corpus evidence — derived from upstream documentation.*
