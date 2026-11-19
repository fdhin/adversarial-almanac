---
name: "qradiolink"
categories:
  - blackarch-radio
version: "0.9.2.2.r0.g6a6966f"
homepage: "https://codeberg.org/qradiolink/qradiolink"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-12-07"
build-system: make
license: GPL-3.0-or-later, LGPL-3.0-or-later, MIT
meta-checked: "2026-11-09"
github-repo: "qradiolink/qradiolink"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# qradiolink

> Multimode SDR voice transceiver with a Qt GUI — analog and digital voice over USRP, RTL-SDR, ADALM-Pluto and LimeSDR hardware.

*No corpus evidence — derived from upstream documentation.*

## What it's for

QRadioLink is a VOIP/radio-over-IP SDR transceiver application built on GNU Radio: one program covers a multimode transceiver (analog FM/SSB voice plus digital voice built on Codec2/FreeDV and MMDVM-based modes), a waterfall/spectrum display and Mumble-based intercom networking. Supported hardware includes Ettus USRP devices, RTL-SDR (receive), ADALM-Pluto via SoapySDR, LimeSDR-mini/LimeNET-Micro, and other gr-osmosdr devices such as HackRF and BladeRF; a LimeRFE frontend can be controlled for power amplification on amateur bands. Development moved from GitHub to Codeberg (the GitHub repository now only points there), with documentation at qradiolink.org/docs. It is a full transceiver — unlike the receive-only decoders in this category it can transmit where the hardware supports it.

## Typical use cases

### Launch the transceiver

```
qradiolink
```

Outcome: the Qt GUI opens; select the SDR device (for example an ADALM-Pluto through SoapySDR or a USRP bus device), pick the operating mode, and the waterfall plus voice controls come up. Operation details for each mode are documented on the project's documentation site.
