---
name: "sakis3g"
categories:
  - blackarch-automation
version: "0.2.0e"
homepage: "http://www.sakis3g.org/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "xdaco/sakis3g"
github-stars: 9
github-pushed: "2013-07-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sakis3g

> Self-contained shell script that brings up a 3G/UMTS/GPRS data connection over a USB or Bluetooth modem with zero configuration — it auto-detects the modem, switches modes and even guesses operator APN settings.

## What it's for

Getting a GSM 3G/UMTS/GPRS modem working on Linux with one command, in the era when NetworkManager/ModemManager support was hit-or-miss. Sakis3G inspects the environment (kernel, distribution, modem, operator), takes care of usb_modeswitch for switchable USB sticks, picks APN settings from its embedded database covering operators in 44 countries, and drives pppd to establish the link — no wvdial, udev or hal configuration needed. It works interactively (a text or GUI menu when run with no arguments), non-interactively (`connect`/`disconnect` actors), and supports chaining actors for one-liners. GSM networks only — CDMA was never supported. Historical status: the last release is 0.2.0e, the project's site (sakis3g.org) is gone (documentation survives on the Wayback Machine, and the wiki's invocation page matches the man page), and 2G/3G networks are being sunset worldwide — so this is a legacy tool for old hardware and old networks, of little use on modern LTE/5G modems.

*No corpus evidence — derived from upstream documentation (archived official wiki).*

## Typical use cases

### Bring up a 3G connection non-interactively

```
sudo sakis3g connect
```

Outcome: the script detects attached USB/Bluetooth modems, switches mode if needed, looks up the operator's APN in its embedded database (asking you for the PIN or APN only when it cannot guess), and establishes the ppp data connection. Exit code and `sakis3g status` afterwards report whether a connection is up. The inverse actor `sudo sakis3g disconnect` tears it down.

### Work through the interactive menu instead

```
sakis3g
```

Outcome: with no arguments the script presents its menu UI — connect with 1-Click, select modem/APN manually, or reach the "more actions" menu — so you can drive a first connection on unknown hardware and see what it detects before automating it with actors.

### Chain actors into a conditional one-liner

Actors can be chained; combined with the `connected`/`disconnected` status actors this gives idempotent reconnect logic:

```
sakis3g connected disconnect || sakis3g disconnected connect
```

Outcome: if a connection is already up it is dropped first, otherwise a new one is established — the exact idiom from the official invocation docs, suitable for cron or an rc script (the packaged `sakis3g-rc` runs `sakis3g silent status` / `sakis3g silent plugged` probes the same way). Add `connect info` (`sakis3g connect info`) to print the IP/operator details after connecting.
