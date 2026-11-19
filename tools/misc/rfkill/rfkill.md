---
name: "rfkill"
categories:
  - wireless
version: "2.42.4-1"
homepage: "https://github.com/util-linux/util-linux"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "util-linux/util-linux"
github-stars: 3249
github-pushed: "2026-11-05"
github-archived: false
github-issues: 446
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2157
gh-activity-checked: "2026-11-09"
---

# rfkill

> util-linux tool to list and software-block or unblock radio transmitters (Wi-Fi, Bluetooth, WWAN, GPS...) through the kernel's rfkill subsystem.

## What it's for

rfkill exposes the kernel's RF kill-switch interface: every radio device carries a software block (which this tool sets and clears) and a hardware block (a physical switch or firmware key, readable but not settable). Its main use is diagnosing "wireless is unavailable" — a soft block is the most common cause, and `rfkill unblock` clears it without a reboot. Devices are addressed by index or by type keyword (`wifi`, `bluetooth`, `wwan`, `gps`, `fm`, `nfc`, ...), so the same command works regardless of device names. It ships with util-linux; output can be formatted raw or JSON for scripting.

## Typical use cases

### List radios and their block state

First stop when a wireless interface refuses to work.

```
rfkill list
```

Outcome: prints one block per radio (e.g. `0: phy0: Wireless LAN`) with `Soft blocked: yes/no` and `Hard blocked: yes/no`; a `yes` in either line explains a missing carrier.

### Get JSON output for scripts

Feed rfkill state into scripts.

```
rfkill -J list
```

Outcome: emits the same listing as JSON (also `-r` for raw, `-n` to drop headings, `-o DEVICE,SOFT,HARD` to pick columns), parseable with `jq`.

### Unblock Wi-Fi

Clear the software block that keeps the interface down.

```
rfkill unblock wifi
```

Outcome: clears the soft block on all Wi-Fi radios so the interface can be brought up; `rfkill unblock all` does the same for every radio. Requires root. A `Hard blocked: yes` persists until the physical switch is toggled.

### Disable a radio

Silence a transmitter on demand.

```
rfkill block bluetooth
```

Outcome: sets the software block on all Bluetooth radios; the kernel kills the transmitter immediately (`rfkill toggle` flips it back).

### Watch for rfkill events

React to hotkeys and hotplug state changes.

```
rfkill event
```

Outcome: streams one line per kernel rfkill uevent (device, type, soft/hard state) as switches are flipped or radios appear — useful to confirm a laptop's Wi-Fi key is actually wired to the subsystem.

*No corpus evidence — derived from upstream documentation.*
