---
name: "airoscript"
categories:
  - blackarch-wireless
version: "45.0a122ee"
homepage: "https://github.com/patrickhno/Airoscript"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: perl
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "patrickhno/Airoscript"
github-stars: 5
github-pushed: "2010-04-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# airoscript

> Menu-driven console wrapper that walks you through aircrack-ng scanning and attacks without typing each command.

## What it's for

Airoscript is a bash text-user-interface for the aircrack-ng suite: it discovers access points, lets you pick a target from a numbered list, and then offers the attacks that fit the selected AP's encryption (WEP/WPA capture, client deauthentication, injection tests, cracking). It does no crypto or capture work itself — it orchestrates `airodump-ng`, `aireplay-ng`, `aircrack-ng` and optionally `mdk3` and `macchanger` in spawned terminal windows. Reach for it when you want the standard aircrack-ng workflow without memorizing flag combinations; skip it when you need non-standard flag combos or scripting, since the menu only exposes the tool's conventional paths. It is a 2008-era companion (targets aircrack-ng 1.0) and must run as root.

## Typical use cases

### Launch the attack menu

Start the interactive session on the interface configured in the config file.

```
sudo airoscript
```

Outcome: after a root check the numbered main menu appears (scan, select target, attack, crack); all capture and injection subprocesses open in windows of the configured terminal emulator. The wireless card is taken from `WIFI` (and the optional separate injection card `IWIFI`) in `/etc/airoscript-ng.conf` — on BlackArch the packaged path is `/etc/airoscript-ng/airoscript-ng.conf`.

### Choose the terminal emulator for spawned windows

Run the menu with an explicit terminal for the aircrack-ng subprocess windows.

```
sudo airoscript xterm
```

Outcome: same menu, but every window airoscript spawns (dumping, injection, deauth) runs in `xterm`; the positional argument accepts one of `xterm`, `urxvt`, `gnome-terminal` or `screen`.

### Point the tool at your cards

Set which interfaces the menu uses before launching.

```
sudoedit /etc/airoscript-ng/airoscript-ng.conf
```

Outcome: editing `WIFI="wlan0"` (monitor-capable card) and, when capture and injection use different radios, `IWIFI="wlan0mon"` makes the menu's scan and attack actions operate on those interfaces; an unset `WIFI` leaves the menu without a working interface.

*No corpus evidence — derived from upstream documentation.*
