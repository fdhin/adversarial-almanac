---
name: "htrosbif"
categories:
  - blackarch-fingerprint
version: "134.9dc3f86"
homepage: "https://github.com/huayra/htrosbif"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-20"
build-system: perl
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "huayra/htrosbif"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 0
github-pushed: "2013-01-25"
github-archived: false
github-issues: 0
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# htrosbif

> Active HTTP server fingerprinter that prods a web server with compliant and spec-breaking requests and matches the quirks of its replies.

## What it's for

htrosbif is an active HTTP server fingerprinting and recon tool: it sends a
wide range of old, new, basic, fancy, spec-compliant and deliberately
spec-breaking requests, characterizes both the well-formed and the deviant
responses, and matches the result against a directory of `.sig` signature
files covering many Apache, IIS, Boa, Cherokee, CUPS and other server builds.
As a side effect it can detect reverse proxies, HTTP load balancers, intrusion
prevention systems and web application firewalls through its overlay
signatures. The target URL must return a 200-series response to a plain GET —
no redirects or authentication. Signatures contain no user data, only header
names and HTTP-level quirks, so they can be captured on one host and matched
elsewhere. Originally written by Erik Inge Bolsø; the packaged Perl tree is
maintained in the Huayra Linux mirror of the retired lkarsten repository.

## Typical use cases

### Identify the web server software

```
htrosbif http://target.example.com/
```

Outcome: probes the URL and prints the top ten matching signatures from
`sigs/`, ranked — the quickest answer to "what is actually serving this site".

### Record a signature for later matching

```
htrosbif -w captured.sig http://target.example.com/
```

Outcome: writes the observed header/quirk fingerprint to `captured.sig`
instead of matching; the file can be sent to a peer or archived as evidence of
how a server behaved on a given date.

### Match a previously captured signature

```
htrosbif -r captured.sig
```

Outcome: runs the signature match offline against the signature directory
without sending any traffic to the target.

### Detect front-end proxies, load balancers and WAFs

```
htrosbif -O overlays http://target.example.com/
```

Outcome: additionally applies overlay signatures (e.g. HAProxy, Pound,
Varnish) to reveal intermediary devices sitting in front of the web server;
add `-a` to list all matches rather than the best ones.

*No corpus evidence — derived from upstream documentation.*
