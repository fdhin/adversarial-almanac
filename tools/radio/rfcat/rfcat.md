---
name: "rfcat"
categories:
  - blackarch-radio
version: "v3.0.0.r3.gcf7a4f9"
homepage: "https://github.com/atlas0fd00m/rfcat"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-08-24"
build-system: python
license: BSD-3-Clause, GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "atlas0fd00m/rfcat"
github-stars: 632
github-pushed: "2026-08-16"
github-archived: false
github-issues: 43
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# rfcat

> Python research toolchain for sub-GHz ISM radio on CC1111 dongles — interactive shell over the YARD Stick One and friends.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rfcat pairs custom firmware for TI CC1111-based USB dongles (YARD Stick One, CC1111EMK, the Chronos watch dongle) with a Python client that exposes the radio as a scriptable object: frequency, data rate, modulation registers, transmit and receive are all API calls. It exists to shorten the loop when analyzing unknown ISM targets — reverse-engineering protocols, replaying captures, or building proof-of-concept transceivers in the 300–928 MHz bands. It needs a supported dongle flashed with the rfcat firmware (flashing is covered in the project's own docs); without the hardware the client has nothing to talk to. The interactive console is an IPython shell, so the full rflib API is available with tab completion.

## Typical use cases

### Open the interactive radio shell and probe the dongle

```
rfcat -r
```

Outcome: an IPython session connects to the dongle; `d.ping()`, `d.discover()` and `print(d.reprRadioConfig())` show the radio is alive and how it is currently configured, and `d.debug()` dumps firmware-side diagnostics. Run as a user with USB access to the dongle (root or the dialout group).

### Receive frames and transmit on a chosen data rate

```
rfcat -r
d.setMdmDRate(19200)
d.RFxmit(b'\x00\x01\x02TEST')
d.RFrecv()
```

Outcome: the modem data rate is set to 19200 baud, a raw test frame is transmitted on the configured frequency, and `d.RFrecv()` blocks until an incoming frame arrives and returns its bytes with a timestamp — the basic send/receive loop for probing an unknown protocol.
