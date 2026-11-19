---
name: "libosmocore"
categories:
  - blackarch-radio
version: "1.14.0"
homepage: "https://osmocom.org/projects/libosmocore/wiki/Libosmocore"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-04-29"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "osmocom/libosmocore"
github-stars: 143
github-pushed: "2026-10-29"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 135
gh-activity-checked: "2026-11-09"
---

# libosmocore

> The shared C libraries underneath every Osmocom mobile-communications project.

## What it's for

This is primarily a library package, not a program: it builds the eight core libraries (libosmocore, libosmovty, libosmogsm, libosmoctrl, libosmogb, libosmocodec, libosmocoding, libosmosim) that Osmocom's cellular stack — OsmoBSC, OsmoBTS, OsmoMSC, OsmoPCU, OsmoSGSN, OsmoTRX, sim tools and friends — all link against: select loops, timers, message buffers, the VTY command-line interface, GSM protocol helpers, GSMTAP, channel coding and SIM/USIM card access. Practically nothing here is used standalone; installing it is a prerequisite for running any Osmocom daemon, and its headers are what you include when writing code against the stack. Third-party SDR tools that dissect GSM (gr-gsm among them) also link parts of it.

Alongside the libraries it ships a handful of small installed utilities for the GSM chores that come up when working with that stack: `osmo-arfcn`, `osmo-auc-gen`, `osmo-aka-verify`, `osmo-config-merge` and `osmo-gsmtap-logsend`.

## Typical use cases

### Convert an ARFCN to uplink/downlink frequencies

Answer the constant "which MHz is channel N?" question for any GSM band.

```
osmo-arfcn -a 25
```

Outcome: prints `ARFCN  25: Uplink 894.8 MHz / Downlink 939.8 MHz` — the band is derived from the channel number itself, so GSM900/DCS1800/PCS1900 channels all resolve correctly. `-u` or `-d` restricts output to one direction, `-f <freq>` does the reverse lookup (frequency to ARFCN), and `-p` handles PCS1900-style numbering ambiguities.

### Generate 2G/3G authentication vectors from SIM credentials

Produce the triplet/quintet an HLR/AuC would compute, using your own Ki and RAND.

```
osmo-auc-gen --3g -k 000102030405060708090a0b0c0d0e0f -o 8ea27a399cf5b8fe1a1c88d41d1e1d1e -r 000102030405060708090a0b0c0d0e0f
```

Outcome: runs Milenage and prints the 3G authentication vector fields (XRES, CK, IK, AUTN) for the given Ki, OPc and RAND; `--2g` instead produces GSM triplets (SRES/Kc via COMP128), and `-s`/`-i` set the sequence number and IND slot. This is how you validate SIM-side crypto or pre-compute vectors for test equipment.

### Verify a USIM's authentication challenge response

Check a captured RAND/AUTN pair against a card's keys.

```
osmo-aka-verify -k 000102030405060708090a0b0c0d0e0f -o 8ea27a399cf5b8fe1a1c88d41d1e1d1e -r 000102030405060708090a0b0c0d0e0f -A 1234567890abcdef1234567890abcdef
```

Outcome: re-computes Milenage from K and OPc, splits AUTN into its components, and reports whether the received MAC matches XMAC — i.e. whether the network challenge was generated for this key set — plus the expected RES, useful when debugging USIM authentication traces.

### Merge config snippets into an Osmocom daemon's config

Apply a small overlay to a large osmo-* configuration without editing it.

```
osmo-config-merge osmo-bsc.cfg overlay.cfg
```

Outcome: writes to stdout the full config with the patch's sections/lines merged into the base file at the matching points — the supported way to keep local lab tweaks separate from a shipped base config for any `osmo-*` daemon.

*No corpus evidence — derived from upstream documentation.*
