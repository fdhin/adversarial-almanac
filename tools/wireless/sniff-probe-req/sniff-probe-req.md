---
name: "sniff-probe-req"
categories:
  - blackarch-wireless
version: "v0.8.0.r29.g0dacf88"
homepage: "https://github.com/SkypLabs/sniff-probe-req"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "SkypLabs/sniff-probe-req"
github-stars: 280
github-pushed: "2026-08-29"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# sniff-probe-req

> ProbeQuest — live sniffer for 802.11 probe requests, revealing which networks nearby devices remember.

## What it's for

sniff-probe-req is the packaged name of ProbeQuest (Paul-Emmanuel
Raoul / SkypLabs): a Python toolkit that listens on a monitor-mode
interface and displays every Wi-Fi probe request within radio range.
Because phones and laptops probe for networks they have joined before,
the output leaks the saved network names of passing devices — the
privacy problem this tool demonstrates. It filters by ESSID list or
regex, by station MAC address, can restrict display to "fake" ESSIDs,
and saves captures to CSV. It only listens: no injection, no
deauthentication, no decryption. The interface must already be in
monitor mode and the tool must run as root; the installed command is
`probequest`.

## Typical use cases

### Show all probe requests in range

Simplest census of probing devices.

```
sudo probequest wlan0mon
```

Outcome: a live list of probe requests — ESSIDs with the probing
station's MAC address; leave it running to watch devices wander past.
Put the interface into monitor mode first (`sudo ip link set wlan0
down && sudo iw dev wlan0 set type monitor && sudo ip link set wlan0
up`, or `sudo airmon-ng start wlan0`).

### Filter for specific network names

Only see probes that mention networks of interest.

```
sudo probequest -e "CoffeeShopWiFi" "Airport_Free_WiFi" wlan0mon
```

Outcome: only probe requests whose ESSID is in the space-separated
list are displayed — a quick way to tell whether the population around
you carries a given SSID in its preferred-network list.

### Match ESSIDs by regular expression

Catch whole families of network names.

```
sudo probequest -r "hotel-.*" --ignore-case wlan0mon
```

Outcome: display is restricted to ESSIDs matching the regex
(case-insensitive here, via `--ignore-case`); everything else is
dropped before it reaches the screen.

### Log captures to CSV for later analysis

Keep a record of an observation window.

```
sudo probequest -o probes.csv wlan0mon
```

Outcome: every displayed probe request is also appended to
`probes.csv`, ready for spreadsheets or counting unique stations after
the capture.

### Watch only "fake" ESSIDs

Focus on probes that name a specific SSID.

```
sudo probequest --fake wlan0mon
```

Outcome: output is restricted to what the project calls fake ESSIDs —
probe requests carrying a specific network name rather than the
broadcast wildcard.

*No corpus evidence — derived from upstream documentation (probequest.readthedocs.io usage page: `probequest` command-line arguments, monitor-mode instructions and examples; console entry point confirmed by the packaged Python project name `probequest`).*
