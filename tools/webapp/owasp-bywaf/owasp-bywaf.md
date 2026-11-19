---
name: "owasp-bywaf"
categories:
  - blackarch-webapp
version: "26.e730d1b"
homepage: "https://github.com/depasonico/OWASP-ByWaf"
packaged-by: [blackarch]
blackarch-builddate: "2014-12-14"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "depasonico/OWASP-ByWaf"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# owasp-bywaf

> Console-based OWASP project for streamlining web application firewall auditing, built as an interpreter plus plugins.

## What it's for

ByWaf (the OWASP ByWaf Project) is a 2013-era command-line web application penetration testing framework focused on WAF auditing: a Python `cmd`-based interpreter — a subclass called the "Wafterpreter" — plus a set of loadable plugins meant to detect, evade, and display vulnerabilities. Plugin options could register `set_`-prefixed callback functions in the interpreter. The project stopped at a beta release, the GitHub repository is gone, and the packaged version runs on Python 2 — treat it as a historical artifact rather than a current toolkit.

## Typical use cases

### Start the Wafterpreter console

```
owasp-bywaf
```

Outcome: drops into the interactive interpreter where plugins are loaded and selected and attack options are set from the prompt (the packaged command wraps `python2 bywaf.py`).

No corpus evidence — derived from upstream documentation (repo deleted; archived OWASP wiki page + package recipe).
