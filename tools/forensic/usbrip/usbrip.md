---
name: "usbrip"
categories:
  - blackarch-forensic
version: "291.5093c84"
homepage: "https://github.com/snovvcrash/usbrip"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "snovvcrash/usbrip"
github-stars: 1185
github-pushed: "2022-10-03"
github-archived: true
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usbrip

> Tracks USB device connection history and policy violations from Linux system logs.

## What it's for

usbrip ("USB Ripper", snovvcrash) reconstructs USB device event history on Linux by parsing journalctl output or `/var/log/syslog*` / `/var/log/messages*` files: for each event it derives connected/disconnected timestamps, host, VID, PID, product, manufacturer, serial number and port. On top of that history it can export JSON dumps, generate a list of trusted (authorized) devices (`auth.json`), and later report "violation events" — devices present in history but absent from the trusted list. It can also resolve VID/PID pairs against the USB ID database, and when installed with its installer script it maintains password-protected 7-Zip event storages via cron. The upstream repository is archived (development frozen since 2022). Caveat: without journalctl, parsed syslog timestamps carry no year unless rsyslog is reconfigured to `RSYSLOG_FileFormat`.

## Typical use cases

### Show USB event history

Reconstruct what was plugged in, and when.

```
sudo usbrip events history -ql -n 100
```

Outcome: prints the latest 100 connect/disconnect events as a compact list (`-l`; use `-t` for a table), with `-q` suppressing the banner and prompts. Filters like `-d`, `--serial`, `--vid`, `--pid` and `-e` (external devices only) narrow the view.

### Build a trusted-device baseline

Define what authorized USB looks like.

```
sudo usbrip events genauth trusted/auth.json -a vid pid
```

Outcome: derives `trusted/auth.json` — the list of authorized devices keyed on VID and PID — from the current event history; run it on a host whose USB usage is trusted to seed the baseline.

### Flag unauthorized USB devices

Detect every off-baseline device ever connected.

```
sudo usbrip events violations trusted/auth.json -a pid -et -c conn vid pid disconn serial
```

Outcome: lists external (`-e`) USB events whose device is NOT in `auth.json` as a table with exactly the chosen columns — the connected/disconnected times and identity of every unauthorized device.

### Identify a device by VID/PID

Enrich an indicator with vendor/product details.

```
usbrip ids search --vid 0781 --pid 5580
```

Outcome: looks the VID:PID pair up in the local USB ID database copy and prints vendor and product details; `usbrip ids download` refreshes that database.

*No corpus evidence — derived from upstream documentation.*
