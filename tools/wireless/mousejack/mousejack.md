---
name: "mousejack"
categories:
  - blackarch-wireless
version: "5.58b69c1"
homepage: "https://github.com/iamckn/mousejack_transmit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
meta-checked: "2026-11-09"
github-repo: "iamckn/mousejack_transmit"
github-stars: 94
github-pushed: "2016-07-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mousejack

> nRF24 wireless mouse/keyboard attack toolkit — discover, sniff, replay, and inject keystrokes over the 2.4 GHz link (MouseJack).

*No corpus evidence — derived from upstream documentation.*

## What it's for

This package ships the transmit side of the MouseJack research: Python scripts for a CrazyRadio PA USB dongle that discover nRF24-based wireless peripherals (pseudo-promiscuous scanning), follow and log a device's decrypted Enhanced Shockburst traffic, replay captured or generated packets to inject keystrokes, and map star networks. Bundled log files hold pre-captured Logitech MK220 keyboard/mouse packets, so injection can be demonstrated without first sniffing a victim — `logs/keys.log` maps key presses to packets and a packet-generator script (`keymapper.py`) builds new keystroke logs interactively. The detection side of the research lives in the separate BastilleResearch mousejack repository; this toolkit extends those tools with replay/transmit.

## Typical use cases

### Discover dongle traffic across channels

Find wireless devices in range with the scanner.

```
sudo ./nrf24-scanner.py -c {1..5}
```

Outcome: sweeps channels 1-5 and prints decoded Enhanced Shockburst packets with their source addresses; `-p A9` restricts promiscuous discovery to addresses with that prefix and `-l` enables the LNA on CrazyRadio PA dongles.

### Follow one device and log its traffic

Sniff a specific mouse or keyboard as it frequency-hops.

```
sudo ./nrf24-sniffer.py -a 8C:D3:0F:3E:B4 -o output.log
```

Outcome: follows the given address across channels and writes every decoded packet to output.log — the format the replay tool consumes.

### Replay captured keystrokes to the dongle

Inject pre-recorded packets against the target address.

```
sudo ./nrf24-replay.py -a 8C:D3:0F:3E:B4 -i logs/keystroke.log
```

Outcome: transmits each packet from the log file to the target address while following its channel hops; with the bundled Logitech MK220 captures this types the recorded keystrokes on the victim machine.

### Map the devices on a star network

Enumerate everything sharing the dongle's address space.

```
sudo ./nrf24-network-mapper.py -a 61:49:66:82:03
```

Outcome: pings all 256 last-byte variations of the given address on each channel (two passes by default) and reports which addresses answer, enumerating the peripherals on the network.
