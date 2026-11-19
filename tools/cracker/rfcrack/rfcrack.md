---
name: "rfcrack"
categories:
  - blackarch-cracker
version: "128.db73830"
homepage: "https://github.com/cclabsInc/RFCrack"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cclabsInc/RFCrack"
github-stars: 549
github-pushed: "2025-01-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rfcrack

> Sub-GHz SDR attack tool: capture/replay, rolling-code bypass, jamming and scanning via Yard Stick One.

## What it's for

A Python RF test bench for attacking devices that communicate over sub-GHz frequencies — IoT gadgets, doorbells, alarm systems, car key fobs — using a Yard Stick One radio (two of them for rolling-code work) through rfcat. It captures live signals and replays them, replays previously saved captures, performs rolling-code bypass (jam while sniffing, then replay the captured code), jams a frequency, and scans bands for activity (common frequency list, custom list, or incremental sweep). Offline it can compare captured signals against a known one and graph them. Captures are saved under `./captures`, device settings templates under `./device_templates`, and scan logs under `./scanning_logs`. It is hardware-bound (Yard Stick One) and licensed for personal use only; `-n` marks functions that work without the radio attached (graphing, log analysis).

## Typical use cases

### Capture and replay a live signal

Sniff a transmission and immediately retransmit it.

```
rfcrack -i
```

Outcome: enters live replay mode — waits for a signal, saves it under `./captures`, and replays it so the target device reacts (doorbell rings, relay clicks) without its original transmitter.

### Replay a saved capture

Send a previously captured payload at a chosen frequency and modulation.

```
rfcrack -s -u ./captures/test.cap -F 315000000 -M MOD_ASK_OOK
```

Outcome: transmits the bits stored in `test.cap` at 315 MHz using ASK/OOK modulation, reproducing the original device command.

### Rolling-code bypass

Block and capture a keyfob transmission so a later legitimate press still works.

```
rfcrack -r -M MOD_2FSK -F 314350000
```

Outcome: with two Yard Sticks, one jams the frequency while the other sniffs the rolling code, then the captured code is replayed; RSSI boundaries can be tuned with `-U`/`-L`. Rolling code is hit-or-miss by nature and requires the fob to be used near both radios.

### Jam a frequency

Deny the channel to the target device.

```
rfcrack -j -F 314000000
```

Outcome: transmits continuously on 314 MHz for the duration of the run, blocking remote commands (alarm disarm, unlock) while the jam is active.

### Scan for active frequencies

Find where a device transmits before attacking it.

```
rfcrack -k
```

Outcome: sweeps the common sub-GHz frequencies and logs any active signals with timestamps under `./scanning_logs`; use `-f 433000000 314000000` to scan a custom list, or `-b -v 5000000` for an incremental 5 MHz-step sweep.

*No corpus evidence — derived from upstream documentation.*
