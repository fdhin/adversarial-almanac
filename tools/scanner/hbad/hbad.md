---
name: "hbad"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://www.curesec.com/"
packaged-by: [blackarch]
blackarch-builddate: "2014-05-04"
build-system: make
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# hbad

> C daemon that runs a deliberately Heartbleed-vulnerable TLS server to test whether connecting clients leak memory (client-side CVE-2014-0160).

## What it's for

hbad ("Heartbleed Analysis Daemon", CureSec GmbH, 2014) inverts the usual Heartbleed check: instead of scanning servers, it listens as an OpenSSL-1.0-era TLS endpoint so you can find out whether client software — a browser, mail client, or anything speaking TLS — is itself susceptible to the Heartbleed bug. It ships with a self-signed `server.crt`/`server.key` pair (installed under `/usr/share/hbad/`) and takes a port plus a heartbeat payload variant. It tests clients only; it is not a server-side Heartbleed scanner.

## Typical use cases

### Start the vulnerable test daemon

```
hbad -p 8443 -t 1
```

Outcome: listens on TCP 8443 with heartbeat payload 0x0000; connect a client to it over TLS, e.g. `openssl s_client -connect 198.51.100.10:8443 -tls1 -debug`, and the daemon shows how the client's heartbeat handling behaves.

### Exercise larger heartbeat payloads

```
hbad -p 8443 -t 3
```

Outcome: runs the same daemon with the 0xffff heartbeat payload (`-t 2` selects 0x00ff), driving the client-side code paths that process oversized heartbeat lengths.

*No corpus evidence — derived from upstream documentation.*
