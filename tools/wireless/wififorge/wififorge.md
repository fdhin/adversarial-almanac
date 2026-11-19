---
name: "wififorge"
categories:
  - wireless
version: "git (2026-11-02)"
homepage: "https://github.com/blackhillsinfosec/wififorge"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "blackhillsinfosec/wififorge"
github-stars: 1233
github-pushed: "2026-11-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 3
---

# wififorge

> Terminal menu that spins up fully virtual Wi-Fi attack labs in mininet-wifi — no radio, no hardware.

## What it's for

WifiForge, by Black Hills InfoSec, is a training sandbox for Wi-Fi attacks. Instead of real radios it uses mininet-wifi's `mac80211_hwsim` virtual interfaces, so an entire airspace — access points, client stations, and your attacker node — runs in software on one Linux machine and nothing is ever transmitted over the air. Pick a lab from a terminal menu and WifiForge builds the network, drops you into per-node tmux terminals to run the attack hands-on (recon, handshake capture, cracking, evil twins, WPS, and more), and tears everything down cleanly when you leave. Labs are small Python modules (metadata plus a `run()` entry point) that the menu discovers automatically, so you can write your own scenarios. It targets Kali/Python 3.9+ and is Apache-2.0 licensed; launching a lab needs root.

## Typical use cases

### Browse the lab menu and launch one

Start the interactive menu and run a lab end to end.

```
sudo wififorge
```

Outcome: opens the lab menu (arrow keys or `j`/`k` to move, `/` to search, Enter to launch); the selected lab's virtual network is built, per-node terminals open for the attack, and everything is torn down when you quit.

### List available labs without launching

See what scenarios are installed before committing to one.

```
wififorge --list
```

Outcome: prints every discovered lab (no root needed, nothing launched) so you can pick a category or difficulty to run next.

### Preview the menu without root

Look around the interface when you don't have sudo.

```
wififorge --allow-non-root
```

Outcome: opens the same menu read-only for browsing; actually launching a lab still requires root, so use this to explore what's available.

### Load labs from a custom directory

Point WifiForge at your own lab modules instead of only the bundled ones.

```
sudo wififorge --labs-dir /path/to/labs
```

Outcome: labs from the given directory are discovered alongside the built-ins (a lab that fails to import shows up disabled rather than crashing the menu), and can be launched like any other.

*No corpus evidence — derived from upstream documentation (repo README, usage section).*
