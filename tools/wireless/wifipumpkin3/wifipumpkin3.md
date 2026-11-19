---
name: "wifipumpkin3"
categories:
  - wireless
version: "1.1.5-0parrot1"
homepage: "https://github.com/P0cL4bs/wifipumpkin3"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "P0cL4bs/wifipumpkin3"
github-stars: 2521
github-pushed: "2024-01-09"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifipumpkin3

> Rogue-access-point MITM framework with a scriptable interactive console, captive-portal phishing and a REST API.

## What it's for

wifipumpkin3 (wp3) mounts a rogue access point and turns it into a full man-in-the-middle position: hostapd-backed AP with internet sharing, rogue DHCP and DNS server, transparent proxies that intercept and modify web traffic, and credential harvesting through captive portals (captiveflask templates, the Phishkin3 MFA-phishing and EvilQR3 QR-phishing scenarios), plus a deauthentication module and a responder3 plugin for LLMNR/NBT-NS capture on the rogue network. Everything is driven from the `wp3` interactive console (`set`, `start`, `stop`, `use`, `jobs`, ...), scriptable via `.pulp` session files or one-liners, and optionally exposed as a RESTful API. It is the successor to P0cL4bs' older [[wifi-pumpkin]] (that repository now redirects to a "deprecated" archive), written by mh4x0f and hosted under the P0cL4bs organization. Linux only, Python 3.8+, requires hostapd and an adapter supporting AP mode.

## Typical use cases

### Interactive rogue AP session

Bring up an open rogue AP from the console.

```
sudo wifipumpkin3 -i wlan0
```

Then in the `wp3 >` prompt:

```
set proxy noproxy
start
```

Outcome: opens the wp3 console bound to wlan0; `set proxy noproxy` disables traffic proxying, and `start` launches the access point with DHCP/DNS — clients that join appear in the session, traffic flows per the active proxy/plugin settings, and `stop` plus `restore` tears the AP down and undoes the iptables rules.

### Non-interactive one-liner

Run a whole session as a single command with semicolon-separated console verbs.

```
sudo wifipumpkin3 -i wlan0 -x "set proxy noproxy; start"
```

Outcome: wp3 executes each verb in sequence without opening the prompt, standing up the same AP configuration as the interactive session — the shape to use from scripts and automation; `-p session.pulp` does the same with the verbs read from a `.pulp` script file instead.

### Enable a capture plugin via a .pulp script

Turn on responder3 credential capture and start the AP from a script file.

```
sudo wifipumpkin3 -i wlan0 -p responder.pulp
```

with `responder.pulp` containing:

```
set plugin responder3 true
start
```

Outcome: the console loads and runs the script, activating the responder3 plugin before starting the AP — clients on the rogue network get LLMNR/NBT-NS poisoned replies and captured hashes/credentials are collected by the plugin, mirroring the repo's bundled `enable_responder3.pulp` example.

### Drive attacks over the REST API

Expose wp3 operations to other tooling.

```
sudo wifipumpkin3 --rest --restport 1337 --username wp3admin --password Password1
```

Outcome: instead of the console, wp3 starts a RESTful API on port 1337 (the default) authenticated with the given credentials, through which access point, proxies, plugins and sessions are managed programmatically — the interface for embedding rogue-AP attacks into larger automation.

*No corpus evidence — derived from upstream documentation (CLI flags verified against the packaged `__main__.py` argparse; console verbs against `core/common/console.py`; `.pulp` syntax against the bundled example scripts).*
