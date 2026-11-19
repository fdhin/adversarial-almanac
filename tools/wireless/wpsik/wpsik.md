---
name: "wpsik"
categories:
  - blackarch-wireless
version: "8.8d3856b"
homepage: "https://github.com/0x90/wpsik"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "0x90/wpsik"
github-stars: 66
github-pushed: "2019-11-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpsik

> Discovers WPS-enabled access points and reports their WPS attributes for attack assessment (scan/pwn modes).

## What it's for

wpsik (by 0x90) is a Python 2 command-line tool, based on Corelabs' WPSIG and devttys0's WPS scripts, that enumerates access points advertising WiFi Protected Setup. It decodes each AP's WPS information element — manufacturer, model, device name, configured/locked state — and presents it in a table so you can judge which WPS implementations are exposed to known WPS attacks. Two modes exist, `scan` and `pwn`, both driving the same scanning engine; `scan` is the documented reporting surface. It supports active probing or passive listening, single-channel or all-channel sweeps, pcap output, MAC spoofing and a log file. It is 2015-era code on Python 2 — modern APs often handle WPS better than this tool assumes.

## Typical use cases

### Sweep for WPS-enabled access points

```
sudo wpsik scan -i wlan0mon
```

Outcome: hops across all channels on `wlan0mon`, sends probe requests (unless `-p` is given for passive mode) and prints a table of every discovered AP with its WPS attributes — the input for deciding which targets are WPS-attackable.

### Scan one channel and keep the evidence

Fix the channel and save both the raw packets and a log of the run.

```
sudo wpsik scan -i wlan0mon -c 6 -o wps-capture.pcap -l wpsik.log
```

Outcome: scans only channel 6 (`-c`), writes every captured packet to `wps-capture.pcap` (`-o`) for later analysis and appends the run's findings to `wpsik.log` (`-l`), in addition to printing the results table.

### Run the attack mode

```
sudo wpsik pwn -i wlan0mon
```

Outcome: invokes the `pwn` mode, which runs the same WPS discovery engine in attack context — its declared options mirror `scan` (including `-b`/`-e` target selectors); in the released code both modes currently execute the same scan. Treat it as the entry point for WPS attack attempts rather than a separate engine.

*No corpus evidence — derived from upstream documentation.*
