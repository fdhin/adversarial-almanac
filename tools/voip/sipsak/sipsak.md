---
name: "sipsak"
categories:
  - blackarch-voip
version: "0.9.8.1"
homepage: "https://github.com/nils-ohlmeier/sipsak/releases"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2021-06-23"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "nils-ohlmeier/sipsak"
github-stars: 176
github-pushed: "2025-05-26"
github-archived: false
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipsak

> Nils Ohlmeier's "SIP Swiss Army Knife" — a compact C utility for stressing and diagnosing SIP servers, from single probes to traceroute, registrar stress and parser torture modes.

## What it's for

Sending a SIP request to a `sip:` URI and examining what comes back. sipsak (by Nils Ohlmeier, nils@sipsak.org) packs several diagnostic personalities behind one binary: default mode sends a single (generated or file-supplied) request and prints the reply status; traceroute mode (`-T`) walks the SIP path with increasing Max-Forwards; message mode (`-M`) sends a short MESSAGE; usrloc mode (`-U`) registers contacts at a registrar at configurable pace, for both bindings management and stress; randtrash mode (`-R`) streams randomly corrupted messages to torture a server's parser; flood mode (`-F`) sends requests as fast as possible. Digest authentication can be attempted with `-a`, and with c-ares compiled in it resolves targets via DNS SRV. It predates and complements newer audit suites: for broad SIP pentesting see [[sippts]], and for structured load generation see [[sipp]].

## Typical use cases

### Check that a SIP server answers

```
sipsak -vv -s sip:user@example.com
```

Outcome: sends an OPTIONS request to the target URI and prints the received replies in full — the first-stop liveness and behaviour check for any SIP server.

### Trace the SIP path

```
sipsak -T -s sip:user@example.com
```

Outcome: behaves like IP-layer traceroute for SIP: requests with growing Max-Forwards expose each signalling hop; hops are only visible for servers that include Warning headers (a documented limitation).

### Register a contact (or stress the registrar)

```
sipsak -U -C sip:user@target.example.com -x 3600 -a Password1 -s sip:user@example.com
```

Outcome: registers the `-C` contact for the account in the target URI with a 3600-second expiry (here inserting a forwarding), answering 401 challenges with the given password; usrloc mode run without those extras hammers the registrar with registrations — the intended registrar stress test.

### Torture the server's parser

```
sipsak -R -s sip:user@example.com
```

Outcome: continuously sends randomly corrupted SIP messages; watch for 500-class responses, dropped requests or an unresponsive server — a quick parser-robustness smoke test (the PROTOOS suite covers the same ground systematically).

### Send a crafted request from a file

```
sipsak -f bye.sip -g '!FTAG!345.af23!TTAG!1208.12!' -s sip:target.example.com
```

Outcome: reads the raw SIP message from `bye.sip`, substitutes the tagged placeholders (`$FTAG$` → `345.af23`, `$TTAG$` → `1208.12`) per the `-g` expression, and sends the result — the way to replay or hand-craft exact messages through sipsak.

*No corpus evidence — derived from upstream documentation.*
