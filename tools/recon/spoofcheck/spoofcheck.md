---
name: "spoofcheck"
categories:
  - blackarch-recon
version: "16.8cce591"
homepage: "https://github.com/bishopfox/spoofcheck"
packaged-by: [blackarch]
blackarch-builddate: "2017-06-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "bishopfox/spoofcheck"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# spoofcheck

> Reads a domain's SPF and DMARC records and judges whether the domain's senders can be impersonated.

## What it's for

Email-spoofing triage for a domain. A domain is spoofable when any of the following hold: no SPF or DMARC record exists; the SPF record never specifies a `~all` or `-all` hard-fail mechanism; or the DMARC policy is `p=none` or nonexistent. spoofcheck resolves the records and returns a verdict per domain, additionally warning when the DMARC configuration sends mail or HTTP requests on failed SPF/DKIM (reporting addresses). It does pure DNS lookups — it never sends mail. The original BishopFox repository is gone; the BlackArch package still installs the Python script behind a `spoofcheck` wrapper.

## Typical use cases

### Check one domain

```
spoofcheck example.com
```

Outcome: prints the domain's SPF and DMARC records and a verdict on whether the domain is spoofable, naming the condition that failed. From a source checkout the invocation is `./spoofcheck.py example.com`.

### Sweep a whole domain list

```
for d in $(cat domains.txt); do echo "== $d"; spoofcheck "$d"; done
```

Outcome: one record dump and verdict per domain — a quick spoofability inventory across a mail estate.

*No corpus evidence — derived from upstream documentation (archived BishopFox/spoofcheck README via the Wayback Machine; packaging verified against the BlackArch PKGBUILD).*
