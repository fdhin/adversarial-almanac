---
name: "wepbuster"
categories:
  - blackarch-wireless
version: "1.0_beta_0.7"
homepage: "https://code.google.com/archive/p/wepbuster/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: perl
license: BSD
meta-checked: "2026-11-09"
github-repo: "google-code-export/wepbuster"
github-stars: 2
github-pushed: "2015-03-13"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wepbuster

> Perl driver that chains the aircrack-ng suite to crack every WEP access point in range, plus a wordlist generator.

## What it's for

WEPBuster (Perl, by Mark Jayson Alvarez, 2009) automates a full WEP audit end to end. Given a channel set it sweeps them with airodump-ng, builds a list of WEP-enabled APs, then walks the list: fake association via aireplay-ng (handling shared-key auth and deauthing to reveal hidden SSIDs), ARP replay to grow the IV count, escalation to the fragmentation attack with packetforge-ng and finally the `-p 0841` rebroadcast attack, and cracking with aircrack-ng once enough IVs exist. Cracked keys land in `owned.txt` in the current directory. It also ships a permutation-based wordlist generator for WPA-PSK dictionaries, and `sort`/`connect` modes that rank cracked APs by ping round-trip time. Scope control comes from `known_ap.txt` (white list) and `bad_ap.txt` (black list) in the working directory. It is a 2009-era tool: it needs the aircrack-ng suite, a wireless card capable of injection, and the channel set is capped by the `$country` variable inside the script (US: 1/6/11, EU: 1/5/9/13).

## Typical use cases

### Audit every WEP access point in range

Sweep the default non-overlapping channels and attack each WEP AP found.

```
sudo wepbuster
```

Outcome: scans the country-default channels with airodump-ng, then processes each discovered WEP AP (associate, ARP replay, fragment/packetforge fallback, crack); every recovered key is appended to `owned.txt` as `BSSID === channel === SSID === WEP key === timestamp`.

### Restrict the sweep to specific channels

Limit the initial airodump-ng scan to chosen channels (still gated by the in-script `$country` setting).

```
sudo wepbuster 1 6
```

Outcome: only the given channels are scanned; cracking proceeds exactly as in the full sweep, with results again written to `owned.txt`.

### Generate a wordlist for WPA cracking

Use the built-in permutation engine instead of a separate generator like crunch.

```
wepbuster permute --charset lower,number --length 8-10 --uniq > wordlist.txt
```

Outcome: writes every permutation of the given character sets (lowercase letters and digits here) with lengths 8–10 and no repeated characters to `wordlist.txt` on stdout; `--filter` (vowel/consonant/length/source/count) trims candidates, and `--merge` concatenates two word lists line by line.

### Rank cracked APs by latency and connect to the best one

Post-process the `owned.txt` results against a reachable host.

```
wepbuster sort example.com
```

Outcome: pings each cracked AP through the given host, writes `owned_rtt.txt` with round-trip time and packet loss per AP, and reorders `owned.txt` accordingly; `wepbuster connect example.com` then joins the first connectible AP.

### Read the embedded manual

The full documentation ships inside the script itself.

```
wepbuster --man
```

Outcome: prints the complete POD manual (modes, wordlist-generator options, the WEP cracking process, white/black list behavior); `wepbuster --help` prints the short option summary.

*No corpus evidence — derived from upstream documentation (packaged Perl script `wepbuster`, Google Code archive).*
