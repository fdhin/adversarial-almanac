---
name: "usbkill-git"
categories:
  - defensive
version: "r213.d3df79e"
homepage: "https://github.com/hephaest0s/usbkill"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur]
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "hephaest0s/usbkill"
github-stars: 4685
github-pushed: "2024-03-01"
github-archived: false
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usbkill-git

> Anti-forensic kill switch: power the machine off the instant a USB device is inserted or removed.

## What it's for

usbkill is a small root-run Python daemon for Linux, BSD, and macOS that polls the USB device list (by default every 250 ms) and, on any change — insertion or removal — runs your configured pre-shutdown commands and immediately powers the machine off. Its role is anti-forensics for encrypted machines: if a laptop is seized while running (attackers and authorities commonly use a "mouse jiggler" to keep it awake), plugging or pulling any USB device triggers shutdown so disk-encryption keys leave memory; a USB stick tied to your wrist turns it into a dead-man's switch. It complements disk encryption and never replaces it — without encryption, shutdown protects nothing. Trusted devices are whitelisted by USB ID in `/etc/usbkill.ini`, and kill events are logged to `/var/log/usbkill/kills.log`. The vault name reflects the Arch packaging (`usbkill-git`); the installed program is `usbkill`.

## Typical use cases

### Arm the kill switch

```
sudo usbkill
```

Outcome: the daemon enumerates the current USB devices and starts polling; any subsequent plug or unplug event that is not whitelisted executes the configured pre-shutdown commands and powers the machine off, with the event appended to `/var/log/usbkill/kills.log`.

### Whitelist your own devices

```
lsusb
```

Outcome: prints each attached device's vendor:product ID (for example `1234:5678`); adding an ID to the whitelist in `/etc/usbkill.ini` lets that device be hot-plugged without triggering the shutdown, which is how you exempt your own keyboard, mouse, or wrist USB key.

### Dry-run the destructive commands without powering off

```
sudo usbkill --no-shut-down
```

Outcome: on a USB change every command you configured still executes — including the destructive ones, so point it at test paths first — but the machine is not shut down; the upstream-intended way to validate a kill configuration safely.

*No corpus evidence — derived from upstream documentation.*
