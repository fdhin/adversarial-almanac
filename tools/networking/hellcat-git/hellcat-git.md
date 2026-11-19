---
name: "hellcat-git"
categories:
  - networking
version: "r45.8873f05"
homepage: "https://github.com/matildah/hellcat"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "matildah/hellcat"
github-stars: 2
github-pushed: "2021-02-07"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hellcat-git

> Netcat variant that evades per-connection rate limits by restarting the TCP connection every k bytes.

## What it's for

hellcat (matildah, 2014) exploits traffic shaping that grants every new TCP connection a fast burst — the first k bytes or t seconds — before throttling down to a lower sustained rate. It transfers data in chunks of exactly the burst size, closing the connection and opening a fresh one for each chunk so the transfer always stays in the fast initial regime, raising average throughput under such shapers. Two symmetric binaries implement it, `hellsend` and `hellrecv`, each invoked as `chunksize dataport controlport [host]`: the side given a host connects out, the side without one listens. Both ends must be given the same chunksize or the transfer ends with incomplete data. This is a research proof of concept from an upstream archived in 2021, not a hardened file transfer tool.

## Typical use cases

### Receive a transfer

```
hellrecv 100000 9001 9002
```

Outcome: listens on data port 9001 and control port 9002 and waits for the sender; accepted chunks are written out as they arrive, one short-lived TCP connection per 100000-byte burst.

### Send a transfer

```
hellsend 100000 9001 9002 target.example.com
```

Outcome: connects to the receiver's control and data ports and pushes the input in 100000-byte bursts, opening a new connection per burst so each one lands inside the shaper's fast initial allowance. The identical chunksize on both ends is what keeps the stream in sync.

*No corpus evidence — derived from upstream documentation.*
