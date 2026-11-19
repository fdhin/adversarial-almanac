---
name: "fluxion"
categories:
  - blackarch-social
version: "v6.32.r8.gf064d24"
homepage: "https://github.com/FluxionNetwork/fluxion"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "FluxionNetwork/fluxion"
github-stars: 6018
github-pushed: "2026-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 92
gh-activity-checked: "2026-11-09"
---

# fluxion

> Bash suite for WPA/WPA2 key recovery through social engineering — a captive-portal phishing attack from a rogue "evil twin" access point, validated against a captured handshake.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A remake of linset that orchestrates the full evil-twin attack against a Wi-Fi network: scan for a target access point, capture a WPA/WPA2 handshake with the `Handshake Snooper` attack, then launch `Captive Portal`, which spawns a rogue AP impersonating the original, a DNS server redirecting all requests to a local web server, and a portal page demanding the Wi-Fi password — while a jammer deauthenticates clients from the real AP to herd them onto the fake one. Every key submitted to the portal is checked offline against the captured handshake; the attack terminates automatically once the correct key is submitted, logs it, and lets clients reconnect. Setup is mostly manual through interactive menus, with an experimental auto-mode covering some parameters. It requires a Linux host (an external Wi-Fi card is recommended) and does not work under WSL. As a phishing tool it belongs with [[zphisher]] — template pages that capture what the victim submits — rather than [[evilginx2]], which reverse-proxies a real site and harvests live session cookies; here the harvested secret is the Wi-Fi key, verified rather than replayed.

## Typical use cases

### Check and install dependencies only

Prepare the host without starting an attack.

```
./fluxion.sh -i
```

Outcome: runs the dependency checker and offers to install anything missing (wireless tooling, hostapd, web-server components), then exits — useful to validate a setup before an engagement.

### Run the handshake-and-portal attack

Drive the full social-engineering attack against a chosen access point.

```
./fluxion.sh
```

Outcome: opens the attack menus — select the wireless interface and channel, pick the target AP, run `Handshake Snooper` until a WPA/WPA2 handshake is captured, then `Captive Portal` to spawn the rogue AP with its DNS server and portal; when a client submits the correct key the attack stops on its own, the key is logged, and clients reconnect to the original AP.
