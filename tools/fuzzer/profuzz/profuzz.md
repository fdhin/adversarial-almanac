---
name: "profuzz"
categories:
  - blackarch-fuzzer
version: "9.aa6dded"
homepage: "https://github.com/HSASec/ProFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2018-09-01"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "HSASec/ProFuzz"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# profuzz

> Scapy-based PROFINET fuzzer that hammers industrial Ethernet devices with malformed DCP, real-time, alarm, and PTCP frames.

## What it's for

ProFuzz is a simple PROFINET fuzzer built on Scapy 2.1.0, written as a 2012 student project at the University of Applied Sciences Augsburg (the HSASec group). It crafts and sends fuzzed PROFINET frames directly on the wire against a device's MAC address: DCP Identify requests (`dcp`), cyclic real-time frames (`pnio`), alarm frames randomized or ordered (`afr`, `afo`), and PTCP (`ptcp`, beta). The command line takes source and destination MAC, frame type, interface, frame count, and a sniffing toggle; a Qt GUI ships alongside the CLI Fuzzer.py. The upstream repository is gone (only a Wayback Machine snapshot remains), and the package wraps the CLI as `profuzz`. Because it writes raw frames it must run as root, on a segment where you are authorized to test the PLC.

## Typical use cases

### Fuzz a device's DCP Identify handling

```
sudo profuzz -w false -s 02:00:00:11:22:33 -d 02:00:00:aa:bb:cc -t dcp -i eth0 -c 100
```

Outcome: sends 100 fuzzed DCP Identity requests from the given source MAC to the target device MAC out of eth0 (the authors recommend `-w false`, sniffing off); watch the device's error counters or capture on a monitor port to spot resets and crashes.

### Fuzz a device's alarm-frame parsing

```
sudo profuzz -w false -s 02:00:00:11:22:33 -d 02:00:00:aa:bb:cc -t afr -i eth0 -c 500
```

Outcome: fires 500 randomly generated alarm frames (`afr`) at the target's alarm parser — swap `-t` for `afo`, `pnio`, or `ptcp` to target ordered alarms, cyclic real-time, or clock-sync handling.

*No corpus evidence — derived from upstream documentation.*
