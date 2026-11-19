---
name: "hotspotter"
categories:
  - blackarch-wireless
version: "0.5"
homepage: "http://www.remote-exploit.org/?page_id=418"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-18"
build-system: make
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/hotspotter"
github-stars: 0
github-pushed: "2025-12-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# hotspotter

> 2004-era probe-request tool that watches for clients hunting common hotspot names and becomes that hotspot for them.

## What it's for

hotspotter (by Max Moser and Joshua Wright of remote-exploit.org) exploits the way Windows XP clients broadcast probe requests for every network in their preferred-networks list. It passively listens for those probes and compares them against a supplied list of common hotspot names (the packaging ships one as `HOTSPOTLIST`); when a client probes for a listed name, hotspotter can switch the card into access-point mode and accept the client's authentication — by default prompting the operator, or automatically with `-a`. Pre- and post-AP-mode hooks (`-r`, `-e`) let you script around the takeover, with the triggering client's details exported as `HS_STAMAC`, `HS_DSTMAC`, `HS_BSSID` and `HS_ESSID`. The tool predates modern supplicant behavior (Windows XP SP1 hardened the worst of it) and supports only the prism54 and hostap drivers, so on current hardware and clients it is of historical and conceptual interest — the preferred-networks disclosure it abuses is better reproduced with modern probe-request collectors paired with a rogue-AP stack.

## Typical use cases

### Show usage and options

```
hotspotter -h
```

Outcome: prints the banner and the full option list (`-i`, `-f`, `-a`, `-e`, `-r`) with the driver caveats; a quick sanity check that the binary runs on your system before you commit a card to it.

### Watch for hotspot probes and prompt per client

```
sudo hotspotter -i wlan0 -f /usr/share/hotspotter/HOTSPOTLIST
```

Outcome: passively monitors probe requests on wlan0 against the bundled hotspot name list; when a client probes a matching SSID you are asked whether to become that access point, keeping a human in the loop on every takeover.

### Auto-accept and script the takeover

```
sudo hotspotter -i wlan0 -f hotspots.txt -a -e "logger client $HS_STAMAC took bait $HS_ESSID"
```

Outcome: matching clients are accepted without prompting, and the post-AP-mode command runs after the AP is switched on with the environment variables `HS_STAMAC`/`HS_ESSID` expanded — in practice you would launch a DHCP server and downstream tooling here rather than a log line; `-r` provides the same hook just before AP mode (e.g. to deauth the client off its real network first).

*No corpus evidence — derived from upstream documentation (options and hook variables verified against the upstream README bundled with the packaged source).*
