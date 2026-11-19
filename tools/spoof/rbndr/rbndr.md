---
name: "rbndr"
categories:
  - blackarch-spoof
version: "9.a189ffd"
homepage: "https://github.com/taviso/rbndr"
packaged-by: [blackarch]
blackarch-builddate: "2017-12-11"
build-system: other
license: GPL3
meta-checked: "2026-11-09"
github-repo: "taviso/rbndr"
github-stars: 764
github-pushed: "2020-01-16"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rbndr

> Simple DNS rebinding service — encode two IPv4 addresses as hex words in a `.rbndr.us` hostname and it randomly answers with either, at very low TTL.

## What it's for

Testing software for DNS rebinding (a TOCTOU flaw: a client authenticates or checks a hostname once, then keeps using it after its address flips). rbndr gives you a rebinding hostname without editing `/etc/hosts` or running a nameserver: the hostname itself carries the payload — `<ipv4-as-hex>.<ipv4-as-hex>.rbndr.us` — and the public service randomly returns one of the two encoded addresses per query with a very low TTL. Software that binds trust to the hostname alone can be walked from your host to a second address (an internal service, localhost) after a rebind. The same repo ships the tiny C nameserver (`rebinder.c`) so you can self-host the service instead of using the public one. It is deliberately non-conforming: A queries only, one question per query.

## Typical use cases

### Resolve a rebinding hostname and watch it flip

```bash
host c633640a.c6336414.rbndr.us
```

Outcome: each query randomly answers with 198.51.100.10 (`c633640a`) or 198.51.100.20 (`c6336414`); repeat it a few times to catch both answers. Dotted quads convert to the hex words directly (198.51.100.10 → c6 33 64 0a), and the repo's `rebinder.html` does the conversion for you.

### Exercise the rebind against vulnerable software

```bash
host c633640a.7f000001.rbndr.us
```

Outcome: this hostname alternates between your host (198.51.100.10) and 127.0.0.1. Point the target software's preflight/access check at it and retry until the check lands on the address you control; the hostname then rebinds to loopback, granting access to a local service that never opted in — the same pattern that bit Flash and Java browser plugins.

### Self-host the rebinding nameserver

```bash
sudo rbndr
```

Outcome: the compiled `rebinder.c` daemon binds UDP 53 and answers A queries with the same two-hex-word semantics, so `<hex>.<hex>.your-domain` works entirely under your control with no dependency on the public rbndr.us service.

*No corpus evidence — derived from upstream documentation.*
