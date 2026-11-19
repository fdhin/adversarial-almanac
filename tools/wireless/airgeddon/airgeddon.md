---
name: "airgeddon"
categories:
  - blackarch-wireless
version: "v12.02.r0.g700ad7d"
homepage: "https://github.com/v1s1t0r1sh3r3/airgeddon"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-26"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "v1s1t0r1sh3r3/airgeddon"
github-stars: 8117
github-pushed: "2026-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 241
gh-activity-checked: "2026-11-09"
---

# airgeddon

> Interactive all-in-one bash menu for auditing wireless networks: capture, DoS, evil twin, WPS, and offline cracking in one place.

## What it's for

airgeddon wraps the whole workflow of a WiFi audit behind numbered interactive menus: it puts a selected interface into monitor mode, then walks through target discovery, WPA/WPA2 handshake and PMKID capture (driving airodump-ng and hcxdumptool under the hood), deauthentication and other DoS attacks, evil-twin/captive-portal setups with credential harvesting, WPS attacks, and handoff of captured material to offline crackers — it invokes [[hashcat]] (or John the Ripper) itself for the cracking step rather than implementing any cryptanalysis. It has no command-line flags at all: everything happens through menus, with language selection and configuration stored on first run, an optional plugin directory, and xterm windows spawned per task (tmux replaces them under Docker). Requirements are bash 4.2+, root, the [[aircrack-ng]] suite (airmon-ng/airodump-ng/aircrack-ng are the essential dependencies), and a monitor-mode-capable card; hashcat, hcxtools, bettercap, and BeEF are optional and unlock additional menu entries when present. It does not bypass anything on its own — its value is orchestrating the underlying tools with correct arguments and sane sequencing.

## Typical use cases

### Launch the interactive audit session

Start a root menu session on the auditing host.

```
sudo airgeddon
```

Outcome: airgeddon checks for root, scans its dependencies (disabling menu entries whose optional tools are missing), optionally checks for script updates, and opens the main menu — select the wireless interface, confirm monitor-mode switching, and then navigate the numbered menus (handshake/PMKID capture, DoS, evil twin, WPS, offline cracking). Run `sudo bash airgeddon.sh` from a source checkout the same way.

### Run inside Docker on a headless host

Audit from a container using the official Kali-based image.

```
docker run --rm -ti --name airgeddon --net=host --privileged --userns=host -v /path/to/dir:/io -e AIRGEDDON_WINDOWS_HANDLING=tmux v1s1t0r1sh3r3/airgeddon
```

Outcome: pulls and starts the official image with host networking and privileged access to the host's wireless stack; `AIRGEDDON_WINDOWS_HANDLING=tmux` makes airgeddon use tmux panes instead of xterm windows so the menus work over SSH, and files (captures, converted hash files) are written to the mounted `/io` directory on the host. On a desktop X11/Wayland host, use `-e DISPLAY="${DISPLAY}" -e XDG_SESSION_TYPE="${XDG_SESSION_TYPE}"` instead of the tmux variable to keep native window handling.

*No corpus evidence — derived from upstream documentation.*
