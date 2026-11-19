---
name: "pwnagotchi"
categories:
  - wireless
homepage: "https://github.com/evilsocket/pwnagotchi"
origin: "awesome-pentest"
recipe: "upstream"
packaged-by: []
build-system: python
github-repo: "evilsocket/pwnagotchi"
github-stars: 9264
github-match: "exact"
gh-checked: "2026-11-14"
github-commits-year: 1
gh-activity-checked: "2026-11-14"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-14"
license: GPL3
---

# pwnagotchi

> A Raspberry Pi "AI" pet that instruments bettercap with an A2C reinforcement-learning agent to get better and better at capturing crackable WPA handshakes around it.

## What it's for

Pwnagotchi (evilsocket) is a firmware image for the Raspberry Pi Zero W that runs bettercap under the control of a deep reinforcement learning agent (A2C with an LSTM policy). Instead of a fixed attack strategy, it tunes bettercap's parameters epoch by epoch to maximize the crackable WPA key material it captures — passively or via authentication/association (deauth) attacks — in the WiFi environments you expose it to. Captured material is written as PCAP files holding any hashcat-supported handshake form, including PMKIDs and full/half WPA handshakes. It shows its mood on an e-ink face (or web UI), and nearby units can detect each other over a custom 802.11 parasite protocol and divide the channels between them. It is an appliance, not a script: you flash it, carry it around, and harvest handshakes off it — development upstream is slow (about one commit a year), so expect the image line to move gently.

## Typical use cases

### Watch the unit headless in your browser

Once the unit is reachable (USB data link or network):

```
http://pwnagotchi.local:8080/
```

Outcome: the web UI renders the unit's face, current channel, seen APs/clients and session stats — the same view as the e-ink display, no screen needed (address and credentials come from the `ui.web` section of `config.toml`).

### Harvest captured handshakes

Pull the crackable material off the unit for offline cracking.

```
scp root@pwnagotchi.local:/root/handshakes/*.pcap .
```

Outcome: copies every handshake PCAP captured so far (the unit stores them under `/root/handshakes`, per `bettercap.handshakes` in its defaults) to your workstation, ready to feed hashcat.

### Force always-on AUTO mode

Make the unit attack even when plugged into a host's USB data port.

```
touch /root/.pwnagotchi-auto
```

Outcome: on next boot the unit skips MANUAL mode and starts operating immediately; normally the data port implies MANU mode (session stats display plus bettercap's own web UI at `http://pwnagotchi.local`), while power-only boots go straight to AUTO and, after roughly 10–15 minutes of loading `/root/brain.nn`, to AI mode.

*No corpus evidence — derived from upstream documentation.*
