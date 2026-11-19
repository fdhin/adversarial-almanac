---
name: "cowpatty"
categories:
  - 802-11
version: "4.8-4"
homepage: "https://github.com/joswr1ght/cowpatty"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "joswr1ght/cowpatty"
github-stars: 240
github-pushed: "2018-12-04"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cowpatty

> Joshua Wright's WPA/WPA2-PSK dictionary and precomputed-PMK cracker for packet captures containing a four-way handshake.

## What it's for

cowpatty audits WPA/WPA2-PSK passphrase strength: give it a packet capture that contains a four-way handshake, a dictionary of candidate passphrases, and the network's SSID, and it reports the PSK when a candidate matches. Because the PBKDF2 key derivation costs 4096 SHA-1 passes per candidate, raw dictionary runs are slow; the bundled genpmk tool precomputes the PMKs for a given SSID once, after which cowpatty validates handshakes against the hash file orders of magnitude faster. It cracks one SSID at a time (the SSID salts the PMK), takes passphrases from a file or stdin, and can check whether a capture holds usable four-way frames without cracking. It does not capture handshakes itself — capture first with airodump-ng or similar. Current interface: `-f` dictionary, `-d` genpmk hash file, `-r` pcap, `-s` SSID, `-c` check-only, `-v`/`-V` verbosity/version.

## Typical use cases

### Dictionary-attack a captured handshake

```
cowpatty -r capture.cap -f wordlist.txt -s "MyNetwork"
```

Outcome: parses the four-way handshake from the capture and tests every candidate against the SSID; on success prints `The PSK is "..."` plus a passphrases-per-second rate. Quote the SSID if it contains spaces.

### Precompute PMKs once per SSID, then crack fast

```
genpmk -f wordlist.txt -d mynetwork.genpmk -s MyNetwork
cowpatty -r capture.cap -d mynetwork.genpmk -s MyNetwork
```

Outcome: genpmk spends the PBKDF2 time once and stores per-candidate PMKs in the hash file; cowpatty then validates the handshake against them (the README's example jumps from ~69 to ~19,000 passphrases/second), and the same hash file serves every future audit of that SSID.

### Verify a capture holds a crackable handshake

```
cowpatty -r capture.cap -s MyNetwork -c
```

Outcome: checks the capture for complete 4-way frames without attempting to crack — tells you whether to re-capture before spending hours of cracking time.

### Feed mangled candidates from John the Ripper

```
john --wordfile=wordlist.txt --rules --stdout:63 | cowpatty -r capture.cap -f - -s MyNetwork
```

Outcome: John applies its mangling rules to the base wordlist and pipes each permutation (up to the 63-character WPA maximum) into cowpatty via `-f -`, expanding coverage without materializing a giant candidate file.

*No corpus evidence — derived from upstream documentation.*
