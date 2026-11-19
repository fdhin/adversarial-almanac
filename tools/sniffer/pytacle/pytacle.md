---
name: "pytacle"
categories:
  - blackarch-sniffer
version: "alpha2"
homepage: "http://packetstormsecurity.com/files/124299/pytacle-alpha2.tar.gz"
packaged-by: [blackarch]
blackarch-builddate: "2022-03-27"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "israelccarvalho/pytacle"
github-stars: 0
github-pushed: "2022-01-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pytacle

> PyGTK front-end that automates GSM frame sniffing and decryption with the airprobe toolchain.

## What it's for

pytacle (Daniel Mende, insinuator.net / ERNW) wraps the airprobe GSM receiver stack in a graphical workflow so an analyst can capture GSM traffic without hand-assembling the airprobe command chain. From the GUI you scan for surrounding cells, select a channel, run the receiver, and generate the Kc key used to decrypt captured traffic; the alpha2 release adds RTL-SDR stick support alongside the original USRP hardware. It is a Python 2 / PyGTK desktop application with no command-line options — everything, including the receiver configuration, happens in the GUI. It does not handle non-GSM protocols, and decryption still depends on obtaining the right Kc.

## Typical use cases

### Launch the GSM sniffing GUI

Start pytacle to configure capture hardware and begin sniffing GSM frames.

```
pytacle
```

Outcome: opens the PyGTK main window, where you select the capture device (USRP or RTL-SDR), scan for nearby cells, pick an ARFCN, and start the airprobe receiver chain; captured GSM frames and the generated Kc are shown through the GUI as the capture runs.

*No corpus evidence — derived from upstream documentation.*
