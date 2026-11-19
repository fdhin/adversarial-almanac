---
name: "heartbleed-git"
categories:
  - scanner
version: "119.v0.2.0.r48.g4a3332c"
homepage: "https://github.com/FiloSottile/Heartbleed"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "FiloSottile/Heartbleed"
github-stars: 2369
github-pushed: "2021-02-24"
github-archived: false
github-issues: 40
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# heartbleed-git

> Go CLI that checks any TLS endpoint — including STARTTLS-wrapped services — for the Heartbleed vulnerability (CVE-2014-0160).

## What it's for

FiloSottile's `Heartbleed` checker, packaged here as a standalone Go binary, is the tool behind the filippo.io/Heartbleed scan site. Given a `host:port` it completes a TLS handshake, sends a malformed heartbeat, and reports whether the peer leaks memory. Besides plain HTTPS it can negotiate STARTTLS for other services when you name the service and supply the correct port. It is a single-purpose detector, not an exploiter: exit code 0 means SAFE, 1 VULNERABLE, 2 ERROR, which makes it convenient to loop over many targets in scripts.

## Typical use cases

### Test an HTTPS endpoint

```
Heartbleed example.com:443
```

Outcome: prints VULNERABLE or SAFE for the host and exits 0 (safe) or 1 (vulnerable), suitable for scripted sweeps over target lists.

### Test a STARTTLS-wrapped service

```
Heartbleed -service="imap" example.com:143
```

Outcome: performs an IMAP STARTTLS negotiation on port 143 and then runs the heartbeat check; the URL form `Heartbleed imap://example.com:143` is equivalent. The correct port must always be specified explicitly.

*No corpus evidence — derived from upstream documentation.*
