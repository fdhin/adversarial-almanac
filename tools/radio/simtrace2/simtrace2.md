---
name: "simtrace2"
categories:
  - blackarch-radio
version: "1079.e6e7739"
homepage: "https://osmocom.org/projects/simtrace2/wiki"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-09-22"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "osmocom/simtrace2"
github-stars: 174
github-pushed: "2026-10-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 29
gh-activity-checked: "2026-11-09"
---

# simtrace2

> Host utilities for Osmocom SIMtrace2 hardware — sniff, forward and MITM the (U)SIM-to-phone interface.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SIMtrace2 is the second-generation Osmocom SIM trace project: open hardware (SAM3-based, schematics published) plus firmware and this host software for working with the (U)SIM card interface. The host side provides a shared library and three tools — enumerate attached devices, capture the card/phone protocol exchange, and run "card emulation" where the physical SIM sits in a PC/SC reader on your workstation while the phone talks to the SIMtrace2 device instead. That enables passive protocol tracing of SIM/USIM traffic, remote SIM setups, and man-in-the-middle experiments on the card interface. Note the upstream explicitly does not support running inside VMs — USB pass-through timing breaks the card protocol.

## Typical use cases

### Enumerate attached SIMtrace2 devices

```
simtrace2-list
```

Outcome: every USB-attached device running simtrace2 firmware is listed with its interface details — the check that the device enumerates and the right udev permissions are in place before starting a trace.

### Trace SIM-to-phone protocol traffic

```
simtrace2-sniff
```

Outcome: with the device between phone and SIM running the trace firmware, the raw card protocol exchange (TPDUs, ATR, status words) is captured and printed as it happens — the passive view of what the phone and card say to each other.

### Present a PC/SC reader SIM to a remote phone

```
simtrace2-cardem-pcsc
```

Outcome: the card-emulation firmware makes the phone believe the SIMtrace2 device is its card, while the actual SIM is read through a local PC/SC reader — the basis for remote-SIM setups and scripted card manipulation during a session.
