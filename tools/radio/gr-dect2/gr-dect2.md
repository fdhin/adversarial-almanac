---
name: "gr-dect2"
categories:
  - blackarch-radio
version: "39.0d973fe"
homepage: "https://github.com/pavelyazev/gr-dect2"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-15"
build-system: cmake
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "pavelyazev/gr-dect2"
github-stars: 121
github-pushed: "2025-03-16"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gr-dect2

> Real-time DECT voice channel decoder built as a GNU Radio module.

## What it's for

Decoding the voice channels of DECT (Digital Enhanced Cordless Telecommunications) in real time and routing the recovered audio to the sound card. It only works where the link is unencrypted — the canonical example is DECT digital baby monitors, which transmit in the clear. It covers the DECT band at 1880–1900 MHz (channels 1881.792–1897.344 MHz) and needs an SDR that covers that band with a sample rate at least twice the DECT data rate (1 152 000 bps). It is not a DECT protocol analyzer for encrypted calls: encrypted traffic cannot be decoded. Interception of phone traffic may be illegal depending on jurisdiction.

The interface is GNU Radio Companion flow graphs plus a QT GUI — there is no standalone CLI binary. After installation the `dect2_*` examples (USRP2/B200, HackRF, SoapySDR, PlutoSDR variants) are opened in GNU Radio Companion and run from there.

## Typical use cases

### Decode unencrypted DECT voice with a HackRF

Run the ready-made HackRF flow graph from the project's examples directory.

```
gnuradio-companion dect2_Hackrf.grc
```

Outcome: the flow graph opens with a QT window containing an RX gain slider, channel and receiver-ID drop-down lists, and a status console. Pressing Run starts receiving; the console lists every DECT part on the air (receiver ID, DECT ID, part type RFP/PP, voice flag). Selecting a part by ID marks it with an asterisk and, when voice is present (a trailing `v`), the decoded audio plays on the sound card.

### Adapt the receiver to another SDR front end

Use the SoapySDR variant (same directory) for SDRs without a dedicated flow graph, or the B200/PlutoSDR graphs for those devices.

```
gnuradio-companion dect2_soapy.grc
```

Outcome: same QT console and voice decoding as the HackRF variant, with the source block swapped; devices that cannot cover 1880–1900 MHz or the required sample rate fail at startup. Parts occasionally change frequency channel, so re-run a periodic manual scan over the channel drop-down to keep tracking a call.

*No corpus evidence — derived from upstream documentation.*
