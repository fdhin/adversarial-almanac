---
name: "skyjack"
categories:
  - blackarch-drone
version: "16.24e3878"
homepage: "https://github.com/samyk/skyjack"
packaged-by: [blackarch]
blackarch-builddate: "2020-05-06"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "samyk/skyjack"
github-stars: 1838
github-pushed: "2017-11-21"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# skyjack

> Samy Kamkar's Parrot AR.Drone hijacker: monitors the air for Parrot-owned MAC addresses, deauthenticates the real owner, and takes over the drone via node-ar-drone.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SkyJack (samyk/skyjack, Dec 2013) autonomously seeks out Parrot AR.Drone quadcopters and takes wireless control of them. The Perl driver repeatedly runs airodump-ng in monitor mode, spots clients associated with access points whose MAC falls in Parrot's OUI ranges (90:03:B7, 00:12:1C, 90:3A:E6, A0:14:3D, 00:26:7E), deauthenticates the true owner with `aireplay-ng -0 3`, then associates and DHCPs onto the drone's own network as the new owner and hands control to a node.js script built on node-ar-drone. It runs fine grounded — any Linux box with a monitor/injection-capable card (the classic build is a Raspberry Pi with an Alfa AWUS036H) — and was designed to fly on a drone that hunts other drones. It only targets Parrot's Wi-Fi-controlled AR.Drone series; a separate 2015 proof of concept in the same repo family extends the approach to 3DR telemetry links, and nothing else. It depends on the aircrack-ng suite, dhclient and node/nodejs.

## Typical use cases

### Hunt and take over a nearby Parrot drone

Run the full seek → deauth → own loop with the packaged control script.

```
sudo skyjack wlan1 wlan0 /usr/share/skyjack/drone_pwn.js
```

Outcome: the first interface is put into monitor mode and airodump-ng runs in ~7-second cycles printing nearby APs and clients; when a client attached to a Parrot-range MAC is found it prints `Disconnecting the true owner of the drone ;)`, fires the deauth, obtains an IP on the drone's network, prints `TAKING OVER DRONE` and starts the node.js control script. The packaged scripts live under `/usr/share/skyjack/` — pass the absolute path, since the built-in default (`drone_control/drone_pwn.js`) is relative to the source tree. While possessed, the drone's live PNG video stream is served on `http://127.0.0.1:8080`.

### Fly the zombie drone with your own control script

Swap in any node-ar-drone program as the third argument.

```
sudo skyjack wlan1 wlan0 ./my_control.js
```

Outcome: identical seek-and-deauth behaviour, but after takeover SkyJack executes your script via node with the hijacked drone as its target — write it against the node-ar-drone client API (takeoff, land, move, attitude) as in the bundled `drone_pwn.js`, `udp-control.js` or `save-video-stream.js` examples.
