---
name: "aircrack-ng"
categories:
  - 802-11
  - top10
version: "1.7-7"
homepage: "https://www.aircrack-ng.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-03-26"
build-system: python
license: GPL-2.0-only, BSD-3-Clause, OpenSSL
meta-checked: "2026-11-09"
github-repo: "aircrack-ng/aircrack-ng"
github-stars: 7879
github-pushed: "2026-10-09"
github-archived: false
github-issues: 453
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 28
gh-activity-checked: "2026-11-09"
---

# aircrack-ng

> The cracking stage of the Aircrack-ng suite: recovers WEP and WPA/WPA2-PSK keys from packet captures with PTW/KoreK statistics or dictionary attacks.

## What it's for

aircrack-ng is the key-recovery binary of the Aircrack-ng suite — the piece that takes capture files (.cap/.pcap, IVS or Hashcat .hccapx) produced by the capture tools (airodump-ng and friends) and turns them into plaintext keys. For WEP it cracks the key statistically from enough collected IVs using the PTW attack (default, needs few data packets) or the classic FMS/KoreK attacks, plus an optional dictionary method; for WPA/WPA2 it validates four-way-handshake captures against a wordlist, a hex-key dictionary, or a precomputed PMK database from airolib-ng, cracking on CPU with automatic SIMD selection. It does not capture or inject packets itself — pair it with airmon-ng/airodump-ng/aireplay-ng for the full attack chain, or hand it a capture you already have to identify networks and test handshakes.

## Typical use cases

### Identify the networks in a capture

```
aircrack-ng capture.cap
```

Outcome: prints the BSSID/ESSID, encryption type, and number of IVs or WPA handshakes found per network, then offers an interactive selection — a quick read of what a capture actually contains before committing to cracking (a cap with a handshake shows the ESSID even when the password stays unknown).

### Crack a WPA/WPA2 handshake with a wordlist

```
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b 00:11:22:33:44:55 capture.cap
```

Outcome: keys the captured four-way handshake against every candidate in the wordlist; on success the PSK is printed (and written to a file with `-l key.txt`). `-b` (or `-e essid`) pins the target when the capture holds several networks, comma-separate multiple wordlists (`-w list1.txt,list2.txt`), and `-` reads candidates from stdin.

### Crack a WEP key with the PTW attack

```
aircrack-ng -z -n 128 capture.cap
```

Outcome: runs the PTW attack (the default) on the collected IVs for a 104/128-bit WEP key; if it fails, add `-K` for the KoreK statistical attacks or raise the fudge factor (`-f 4`) to brute-force harder at the cost of time — a dictionary of hex keys works too (`-w h:hexkeys.txt -n 64`).

### Hand the handshake to a GPU cracker

```
aircrack-ng capture.cap -j handshake.hccapx
```

Outcome: instead of CPU cracking, exports the captured WPA handshakes to Hashcat's HCCAPX format for `hashcat -m 22000` on GPUs (older hashcat releases take `-J` HCCAP instead, `-E` exports an Elcomsoft EWSA project); the target network is chosen interactively or via `-e`/`-b`.

### Crack against a precomputed PMK database

```
aircrack-ng -r pmkdb capture.cap -e target
```

Outcome: validates the handshake against an airolib-ng database of precomputed pairwise master keys, trading capture-time work for near-instant passphrase checks — orders of magnitude faster than a plain wordlist when the SSID's PMKs were precomputed; cannot be combined with `-w`.

*Corpus-grounded — handshake wordlist cracking and capture inspection mined from corpus evidence; target selection, WEP, HCCAPX export and airolib usage verified against the upstream man page.*
