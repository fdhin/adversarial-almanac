---
name: "speedpwn"
categories:
  - blackarch-cracker
version: "8.3dd2793"
homepage: "https://gitorious.org/speedpwn/"
packaged-by: [blackarch]
blackarch-builddate: "2014-08-25"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "aur-archive/speedpwn"
github-stars: 0
github-pushed: "2015-08-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# speedpwn

> Active WPA/WPA2 brute-forcer that attacks weak ISP default keys with no client and no captured handshake.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Demonstrating weak standard key generation in ISP-labelled routers. Unlike
offline handshake cracking, speedpwn talks to the access point itself: it
sends 802.11 authentication, association and handshake attempts for each
password candidate using aircrack-ng's osdep library for frame injection, so
no associated client and no captured 4-way handshake are needed. Two attack
modes: `-1` exploits the known Telekom/Arcadyan Speedport default-key scheme
directly, and `-0` runs a plain wordlist attack. The bundled
`speedport-arcadyan.pl` generates the matching candidate list for a given
SSID/MAC pair. Speed is tuned with `--agressiv`/`--medium`/`--slow` (the
spelling is the tool's own) and `-s` spoofs the source MAC. Experimental
2011-era research code: channels 1-13 only, and its original gitorious.org
upstream is gone — the BlackArch package pins a 2014 build of it. Not to be
confused with a same-named Ruby Thomson/SpeedTouch password *generator* by a
different author.

## Typical use cases

### Attack a Telekom/Arcadyan Speedport with the built-in keyspace

Exploit the vendor's default password scheme with no wordlist required.

```
speedpwn -1 -e WLAN-ABCD -b 02:11:22:33:44:55 -c 6 wlan0mon
```

Outcome: generates the Arcadyan default-key candidates internally and performs
the full 802.11 auth/assoc/key-exchange cycle for each against the AP on
channel 6, printing the passphrase when an attempt completes the WPA
handshake; the replay interface (`wlan0mon`, monitor mode) is always the last
argument.

### Wordlist attack against any WPA/WPA2 network

Test a prepared candidate list actively.

```
speedpwn -0 -w candidates.txt -e WLAN-ABCD -b 02:11:22:33:44:55 -c 6 --agressiv wlan0mon
```

Outcome: walks `candidates.txt`, one association attempt per line, at the
aggressive timeout profile (250 ms class timeouts; `--slow` is the
conservative profile), and reports the working passphrase on success.

### Generate the matching Speedport candidate list

Produce the wordlist for `-0` from the AP's SSID and MAC alone.

```
perl speedport-arcadyan.pl WLAN-ABCD 02:11:22:33:44:55 > candidates.txt
```

Outcome: writes the 4096 `SP-XXXXXXXX` default-key candidates derived from the
last SSID characters and MAC bytes to `candidates.txt`, ready to feed to
`speedpwn -0`.
