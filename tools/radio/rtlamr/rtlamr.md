---
name: "rtlamr"
categories:
  - blackarch-radio
version: "197.03369d1"
homepage: "https://github.com/bemasher/rtlamr/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2021-04-27"
build-system: go
license: AGPL
meta-checked: "2026-11-09"
github-repo: "bemasher/rtlamr"
github-stars: 2556
github-pushed: "2026-11-06"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 31
gh-activity-checked: "2026-11-09"
---

# rtlamr

> Receiver for 900 MHz ISM smart-meter ERT messages (SCM/SCM+/IDM/R900) using an RTL-SDR dongle.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rtlamr is a Go receiver for utility smart meters that broadcast consumption in the 900 MHz ISM band using Itron's Encoder Receiver Transmitter (ERT) protocol family — and Neptune R900 water meters. It attaches to a running `rtl_tcp` instance (it does not open the dongle directly) and decodes the supported message types: `scm` and `scm+` (total consumption), `idm` and `netidm` (interval data, including net meters that report generation), and `r900`/`r900bcd` (consumption plus leak flags). Typical use is passively logging your own household's meters to watch consumption; the project explicitly asks that it not be used to profile other people.

## Typical use cases

### Start the receiver against a local dongle

```
rtl_tcp
rtlamr
```

Outcome: `rtl_tcp` (from rtl-sdr) exposes the dongle on the default port; rtlamr connects, tunes to 912 MHz and begins printing decoded ERT messages — each line shows the meter ID, commodity type and consumption as meters in the neighborhood transmit.

### Decode only specific message types

```
rtlamr -msgtype=idm,scm+
```

Outcome: only Interval Data Messages and extended Standard Consumption Messages are decoded; everything else is dropped, which quiets the output when you know your meter's message type from its FCC ID label.

### Log machine-readable output from a remote receiver

```
rtlamr -format=json -server=198.51.100.10:1234
```

Outcome: decoded messages are emitted as JSON objects (meter ID, consumption, intervals) suitable for piping into `jq` or a collector, and `-server` points at an rtl_tcp instance running on another host so the radio can sit near the meter while analysis happens elsewhere.
