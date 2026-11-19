---
name: "fockcache"
categories:
  - blackarch-webapp
version: "10.3e7efa9"
homepage: "https://github.com/tismayil/fockcache"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-26"
build-system: go
license: GPL2
meta-checked: "2026-11-09"
github-repo: "tismayil/fockcache"
github-stars: 110
github-pushed: "2020-02-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fockcache

> Minimal tester for web cache poisoning via X-Forwarded-Host and X-Forwarded-Scheme injection.

## What it's for

FockCache (by tismayil, Go) performs a quick check for the classic unkeyed cache-poisoning vectors described in PortSwigger's practical web cache poisoning research: it requests the target with an attacker-influenced `X-Forwarded-Host` header (then `X-Forwarded-Scheme`) and watches for the injected value being reflected into the cached response. It is a fast yes/no triage for that one poisoning class — not a general cache-deception detector, parameter fuzzer or scanner, and it tests only these two headers.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Test a host for header-based cache poisoning

Check whether the cache stores a poisoned response.

```
fockcache --hostname target.example.com
```

Outcome: prints a baseline check, then fires ten requests per vector injecting `X-Forwarded-Host` and `X-Forwarded-Scheme`; any successful reflection is flagged `Focked: <URL>` on stdout — the poisoned URL to serve to victims.
