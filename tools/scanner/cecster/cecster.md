---
name: "cecster"
categories:
  - blackarch-scanner
version: "5.15544cb"
homepage: "https://github.com/nccgroup/CECster"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/CECster"
github-stars: 32
github-pushed: "2013-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cecster

> GUI harness for security-testing the HDMI CEC and HEC protocols through a USB-CEC bridge.

## What it's for

CECSTeR (NCC Group's Consumer Electronics Control Security Testing Resource, by Andy Davis) sends crafted HDMI CEC (Consumer Electronics Control) and HEC (HDMI Ethernet Channel) traffic at a device under test — TVs, amplifiers, and anything else on the HDMI chain — to probe how it parses and reacts to CEC opcodes. The testing methodology behind it is documented in the "HDMI Ethernet Channel" whitepaper shipped in the repository. It is a wxPython GUI driven through a RainShadow USB-CEC bridge (over pyserial); there is no batch or scriptable CLI — you pass the bridge's serial port and work from the interface.

## Typical use cases

### Launch against the USB-CEC bridge

```
cecster /dev/ttyUSB0
```

Outcome: opens the CECSTeR GUI bound to the bridge on that serial port (Windows form: `CECSTeR.py COM3`); from the interface you craft and send CEC opcodes to the device under test and observe its behaviour, following the whitepaper's test cases.

*No corpus evidence — derived from upstream documentation.*
