---
name: "bluemaho"
gentoo-category: "net-wireless"
version: "090417-r2"
homepage: "http://wiki.thc.org/BlueMaho"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "zenware/bluemaho"
github-stars: 67
github-pushed: "2016-06-28"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluemaho

> wxPython GUI shell bundling a suite of Bluetooth scanning, tracking and attack tools for auditing the security of BT devices.

## What it's for

BlueMaho is a GUI front-end (wxPython, published via the THC wiki) that drives a bundled collection of Bluetooth tools and exploits: scanning and tracking of discovered devices with SDP records and vendor lookup, loop scanning with sound alerts on new devices, OBEX file sending, changing the local adapter's name/class/mode/BD_ADDR, and a results database with statistics. It can use two dongles at once — one looping scans while the other runs tools. The bundled arsenal includes bluebugger, bluePIMp, BSS (Bluetooth Stack Smasher), redfang, carwhisperer, psm_scan/rfcomm_scan, atshell and more. It is a 2009-era Python 2 toolkit: the command line only launches the GUI, configuration lives in `config/default.conf`, and most bundled exploits target long-patched firmware — useful as a lab harness for BT auditing workflow, not against modern devices.

## Typical use cases

### Launch the GUI scanner

```
bluemaho.py
```

Outcome: opens the wxPython main window with the local-device list and the bundled tool/exploit pickers; scans populate the device grid, and discovered devices are appended to bluemaho.log (path and commands configurable in `config/default.conf`).

### Launch with verbose console output

```
bluemaho.py -v
```

Outcome: same GUI, but console output is echoed verbosely (stdout/stderr redirected to the terminal) — useful to watch the underlying tool invocations (hcitool, sdptool, bundled scanners and exploits) when diagnosing why a scan or an exploit does not fire.

*No corpus evidence — derived from upstream documentation.*
