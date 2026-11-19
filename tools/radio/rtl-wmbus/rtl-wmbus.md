---
name: "rtl-wmbus"
categories:
  - blackarch-radio
version: "1.1.0.r0.g34684e6"
homepage: "https://github.com/xaelsouth/rtl-wmbus"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-01"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "xaelsouth/rtl-wmbus"
github-stars: 285
github-pushed: "2026-11-06"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7
gh-activity-checked: "2026-11-09"
---

# rtl-wmbus

> Wireless M-Bus decoder that turns an RTL-SDR dongle into a T1/C1/S1 smart-meter receiver.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rtl-wmbus demodulates Wireless M-Bus (the European smart-meter radio standard) straight from I/Q samples: FSK demodulation, DC-offset removal, clock recovery and T1, C1 and S1 mode packet decoding in plain C. It reads a raw sample stream on stdin — normally piped live from `rtl_sdr` on 868.95 MHz — and prints one semicolon-separated line per telegram (mode, CRC status, timestamp, RSSI, link-layer ID, datagram). It is light enough for a Raspberry Pi. This is the actively maintained continuation of the original rtl-wmbus project; it can also consume other SDRs through `rx_sdr`/SoapySDR. Decode only — it cannot talk to meters, and interpreting the datagram payload into readings is a job for [[wmbusmeters]].

## Typical use cases

### Listen live on 868.95 MHz

```
rtl_sdr -f 868.95M -s 1600000 - 2>/dev/null | rtl_wmbus
```

Outcome: the dongle streams 1.6 MS/s I/Q (a multiple of 800 kHz as required by the demodulator) and every decoded meter telegram is printed as it arrives; use `-f` slightly off 868.95M if your dongle has a frequency offset.

### Capture now, decode later

```
rtl_sdr samples.cu8 -f 868.95M -s 1600000
cat samples.cu8 | rtl_wmbus
```

Outcome: the I/Q stream is saved to disk first (a `tee` in the pipe can save and decode at the same time); replaying the file through rtl_wmbus decodes the same telegrams without a dongle attached, which is how recorded captures are shared and re-processed.

### Handle a higher sample rate with decimation

```
rx_sdr -f 868.95M -s 4000000 - 2>/dev/null | rtl_wmbus -d 5
```

Outcome: the SDR runs at 4 MS/s and `-d 5` decimates by five back to the 800 kHz symbol grid the decoder expects, trading CPU for wider hardware sample-rate compatibility.

### Count clean telegrams in a capture

```
cat samples.cu8 | rtl_wmbus 2>/dev/null | grep "[T,C,S]1;1;1" | wc -l
```

Outcome: filters the semicolon output to lines whose mode is T1, C1 or S1 with both CRC and 3-out-of-6 checks passing (`MODE;CRC_OK;3OUTOF6OK` all set to 1) and prints the count — a quick signal-quality check on a capture.
