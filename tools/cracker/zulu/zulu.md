---
name: "zulu"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://sourceforge.net/projects/zulu-wireless/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# zulu

> Command-line 802.11 frame generator — an "hping for wireless" — that crafts arbitrary management, control, and data frames, including protocol-violating ones.

*No corpus evidence — derived from the packaged upstream source.*

## What it's for

Zulu (C; Anmol Sheth and Damon McCoy) generates raw 802.11 frames from the
command line for debugging and probing wireless networks: beacons, probe
request/response, deauthentication, disassociation, RTS/CTS, PS-Poll,
authentication, junk and data frames — 18 frame types in all. Individual
header fields are settable per frame: source/destination MACs, sequence
number, duration, delay, SSID, channel, and the WEP/retry/fragment/more
bits, so frames that violate the IEEE 802.11 protocol can be produced on
purpose to test driver and IDS behaviour. It requires a card in monitor
mode driven by madwifi-ng (Atheros) or partially prism drivers and is
2006-era — expect no support for modern mac80211 hardware. It does not
crack anything itself; it injects and (with `-t 4`) sniffs frames.

## Typical use cases

### Inject deauthentication frames

Send deauth management frames to a target station or the whole BSS.

```
zulu -t 15 -i ath0 -d 000102030405 --channel 6 -n 100 7
```

Outcome: transmits 100 deauthentication frames (`-n 100`) with reason code
`7` (trailing positional argument) from interface `ath0` to the given
destination MAC (hex digits, no colons; `ffffffffffff` broadcasts and
avoids retransmission waits), tuning the card to channel 6 first; a
monitor-mode capture on that channel shows the injected frames and the
station being knocked off.

### Forge fake beacon frames

Announce a counterfeit access point.

```
zulu -t 1 -i ath0 --ssid evil-ap 100 11
```

Outcome: injects beacon frames for SSID `evil-ap` (first positional) with
a beacon interval of `100` and channel `11` (second and third
positionals); wireless scanners in range start listing `evil-ap` as an
open network, which confirms frame injection works end to end.

### Probe a network with junk and data frames

Test driver robustness or IDS alerting with malformed traffic.

```
zulu -t 3 -i ath0 -s 000102030405 -d ffffffffffff 512
zulu -t 6 -i ath0 --to_ap --null_data -n 50
```

Outcome: the first command sends a junk frame of 512 bytes (trailing
positional) from a forged source MAC to broadcast; the second sends 50
null-data frames marked to-DS — deliberate protocol violations visible in
a sniffer as malformed/odd frames, useful for verifying detector coverage.

### Sniff frames on the injection interface

Use zulu's built-in monitor mode to observe the channel.

```
zulu -t 4 -i ath0
```

Outcome: switches zulu into sniff mode, printing frames seen on `ath0` to
the console — handy for confirming on the same card that the frames
injected by the commands above actually hit the air.
