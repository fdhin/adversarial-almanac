---
name: "torpy"
categories:
  - blackarch-networking
version: "60.ebf000c"
homepage: "https://github.com/torpyorg/torpy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "torpyorg/torpy"
github-stars: 434
github-pushed: "2023-04-21"
github-archived: false
github-issues: 21
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# torpy

> Pure-Python Tor client — speaks the Tor protocol directly (no Tor daemon and no Stem required), offering console HTTP fetches, a SOCKS5 proxy and a `requests` adapter.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Getting Tor connectivity from a Python codebase or a shell without installing the official Tor client. Torpy implements the protocol itself: it bootstraps from the directory authorities, builds circuits (default 3 hops) and opens streams, logging each step to the console. It supports v2 onion services with the Basic and Stealth authorization protocols, ships a `TorHttpAdapter` for the `requests` library and a dependency-free urllib opener, and includes SOCKS5 support. It is a client implementation by an independent project — not maintained by or guaranteed by The Tor Project — and should not be treated as a hardened anonymity solution for high-risk use.

## Typical use cases

### Fetch a URL through Tor from the shell

```
torpy-cli --url https://example.com --header "User-Agent" "curl/8.0"
```

Outcome: loads/creates the cached network status, connects to a guard node, builds a 3-hop circuit and issues the request through it, printing each protocol step and finally the response status and headers. (Upstream names the script `torpy_cli`; the packaged binary is `torpy-cli`.)

### Download an onion page to a file

```
python -m torpy --url http://xxxxxxxxxxxxxxxx.onion --to-file index.html
```

Outcome: resolves and fetches the onion service through a fresh circuit (including the v2 hidden-service rendezvous, with client authorization if configured) and writes the response body to `index.html`.

### Run a local SOCKS5 proxy over Tor

```
torpy-socks -p 1050 --hops 3
```

Outcome: starts a SOCKS5 proxy on 127.0.0.1:1050 that routes every connection through 3-hop Tor circuits. Point any SOCKS5-capable application at it (`--socks5 127.0.0.1:1050` style settings) and its traffic exits via Tor. (Upstream name: `torpy_socks`.)
