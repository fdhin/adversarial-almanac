---
name: "yate-bts"
categories:
  - blackarch-radio
version: "6.1.0"
homepage: "https://yatebts.com/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-07-04"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "ctxis/yate-bts"
github-stars: 21
github-pushed: "2018-12-01"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# yate-bts

> Open-source GSM base-station software — YateBTS runs as a module of the Yate telephony engine to operate a 2G network.

*No corpus evidence — derived from upstream documentation.*

## What it's for

YateBTS implements a GSM base station in software (non-DSP approach to the GSM protocol stack), usable standalone for a private 2G test network or connected to telephony infrastructure through Yate's SIP/IMS/SS7 support. It is not a standalone program: it is a module loaded by the Yate telephony engine, configured primarily through `ybts.conf` — at minimum the radio band and ARFCN to operate on, plus the `[transceiver]` path selecting the radio backend (`transceiver-uhd` for USRP, `transceiver-rad1`, `transceiver-usrp1`, ...). Operating a BTS involves radio licensing in most jurisdictions; the upstream explicitly targets lab/research use. Canonical sources live at yatebts.com; the GitHub field here is a third-party research fork of the same codebase.

## Typical use cases

### Bring up the BTS inside Yate

```
yate
```

Outcome: with Yate configured to load the ybts module and `ybts.conf` set (band/ARFCN, transceiver path matching your SDR hardware), starting the Yate engine launches the BTS: the transceiver begins transmitting on the configured ARFCN and nearby GSM handsets can see and register with the network; Yate's log output shows the module's startup and registration events.
