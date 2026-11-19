---
name: "rtl"
categories:
  - blackarch-radio
version: "25.12+400.r4098.20261020.406f9a33"
homepage: "https://github.com/merbanan/rtl_433"
packaged-by: [nixpkgs]
meta-checked: "2026-11-09"
github-repo: "merbanan/rtl_433"
github-stars: 7915
github-pushed: "2026-10-27"
github-archived: false
github-issues: 155
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 407
gh-activity-checked: "2026-11-09"
---

# rtl

> rtl_433 — general-purpose ISM-band receiver that decodes hundreds of 433/868/315/345/915 MHz sensor and device protocols from an SDR.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rtl_433 (the program this package installs, despite the bare package name) is a generic data receiver for the 433.92 MHz, 868 MHz, 315 MHz, 345 MHz and 915 MHz ISM bands. It demodulates OOK/FSK from RTL-SDR dongles, SoapySDR devices or an rtl_tcp server, and decodes into structured output more than two hundred device protocols — weather stations, remote switches, tire-pressure sensors, doorbells, alarm contacts and the like. Version numbering is year.month. It decodes and logs; it does not transmit. A `rtl_433.conf` file (searched in the working directory, `$HOME/.config/rtl_433/` and the system config dir) can hold device and output settings so daily invocations stay short.

## Typical use cases

### Listen with defaults on 433.92 MHz

```
rtl_433
```

Outcome: the first RTL-SDR dongle is opened at 433.92 MHz with auto gain; every recognized protocol prints its decoded fields (channel, temperature, IDs, button state) as key/value lines on stdout, so a quick run shows what devices are on the air nearby.

### Restrict to one protocol and band

```
rtl_433 -f 868.3M -R 18
```

Outcome: only protocol 18 (a specific sensor family) is decoded on 868.3 MHz; `-R` can be repeated or negated (`-R -18`) to trim a busy band down to the devices you care about, cutting false decodes from look-alike protocols.

### Log decoded output as JSON and CSV

```
rtl_433 -F json -F csv:log.csv
```

Outcome: each decoded message is emitted twice — as a JSON object on stdout for piping into `jq` or a collector, and appended as a CSV row to `log.csv` for spreadsheet or time-series analysis; other sinks include mqtt, influx, syslog and an HTTP server (`-F help` lists them).

### Analyze an unknown signal

```
rtl_433 -A
```

Outcome: the pulse analyzer replaces decoding and prints pulse/sync/gap width histograms and estimates of modulation parameters for each burst — the starting point for identifying an unsupported device and writing a new decoder spec (`-X`).

### Replay a saved I/Q capture

```
rtl_433 -r samples.cu8
```

Outcome: the receiver is bypassed and the demodulator/decoders run over the recorded file (`.cu8` and friends), reproducing exactly what a live capture would have decoded — useful for regression-testing decoders against known signals.
