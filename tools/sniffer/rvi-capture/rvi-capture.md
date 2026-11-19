---
name: "rvi-capture"
categories:
  - blackarch-sniffer
version: "14.a2e129b"
homepage: "https://github.com/gh2o/rvi_capture"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "gh2o/rvi_capture"
github-stars: 219
github-pushed: "2024-03-26"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rvi-capture

> rvictl for Linux and Windows: capture packets sent or received by a USB-connected iOS device.

## What it's for

rvi-capture (upstream rvi_capture.py by gh2o) reproduces macOS's rvictl Remote Virtual Interface facility on Linux and Windows: it exposes the network traffic of a stock, non-jailbroken iOS device as a pcap/pcapng stream you can open in Wireshark. It talks to the phone over usbmuxd (Linux) or AppleMobileDeviceService (Windows) via libimobiledevice, so the device must be USB-connected and paired, and usbmuxd must be running. Each interface appears separately in the capture (en0 Wi-Fi, pdp_ip0 cellular, ipsec* for VoLTE), letting you filter per interface in Wireshark. It is not an over-the-air or remote sniffer — it only sees traffic of devices tethered by USB to the machine running it.

## Typical use cases

### Capture an iOS device's traffic to a file

Record all packets sent and received by the first iOS device found.

```
rvi-capture --format pcapng capture.pcapng
```

Outcome: writes a pcapng capture of the device's traffic to capture.pcapng, with each frame tagged by its originating interface (filter on frame.interface_name in Wireshark to isolate Wi-Fi vs cellular); stop with Ctrl-C.

### Stream device traffic live into Wireshark

Pipe the capture straight into Wireshark instead of writing a file.

```
rvi-capture - | wireshark -k -i -
```

Outcome: Wireshark opens immediately and shows the device's live traffic as it happens; no intermediate file is created.

### Target one specific device

When several devices are attached, select one by UDID.

```
rvi-capture --udid <UDID> --format pcap capture.pcap
```

Outcome: captures only the named device to a legacy pcap (for tools that do not read pcapng); omit --udid to use the first device found.

*No corpus evidence — derived from upstream documentation.*
