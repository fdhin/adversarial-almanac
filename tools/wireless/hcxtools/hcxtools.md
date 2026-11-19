---
name: "hcxtools"
categories:
  - wireless
homepage: "https://github.com/ZerBea/hcxtools"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [arch, nixpkgs]
build-system: make
license: MIT
github-repo: "ZerBeA/hcxtools"
github-stars: 2475
github-match: "exact"
gh-checked: "2026-11-13"
github-commits-year: 166
gh-activity-checked: "2026-11-13"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
---

# hcxtools

> Converter suite that turns WLAN capture files (pcapng/pcap/cap) into hashcat- and John-ready hash files — the analysis half of the hcxdumptool pipeline.

## What it's for

hcxtools (ZerBeA) is a small set of C tools that convert packets from capture files into hash files for hashcat and John the Ripper; they are endorsed by hashcat. The centerpiece is `hcxpcapngtool`, which extracts WPA-PMKID/EAPOL material into hashcat's hash mode 22000 (plus EAP-MD5, LEAP/MSCHAPv2 and TACACS+ side outputs), and the suite's helper tools then work on those hash files: `hcxhashtool` filters and groups them, `hcxpsktool` generates weak-PSK candidate lists, `hcxeiutool` prepares extracted wordlists for rule-based attacks, `hcxpmktool` verifies a PSK/PMK against a hash line, and `hcxhash2cap` converts hashes back to capture files. `wlancap2wpasec` uploads captures to the wpa-sec cracking statistics service and `whoismac` resolves MAC vendors. It is the conversion/analysis half of a two-part workflow: the capture half (PMKID/handshake harvesting off the air) is [[hcxdumptool]], whose note covers that side — keep `hcxpcapngtool` and `hcxdumptool` versions in sync. hcxtools do NOT crack anything themselves (that is hashcat/JtR work), do not handle WEP (aircrack-ng) or WPS (Reaver/Bully), and do not decrypt traffic (tshark/Wireshark). By design they are analysis tools: everything is converted by default and unwanted hashes are filtered out afterwards. Linux only — Windows, macOS, Android and wrappers are explicitly unsupported.

## Typical use cases

### Convert a capture to hashcat/JtR hashes

Turn a pcapng/pcap/cap dump into the crackable hash file.

```
hcxpcapngtool -o capture.22000 capture.pcapng
```

Outcome: writes `capture.22000` in hashcat's WPA-PBKDF2-PMKID+EAPOL format (hash mode 22000) and prints a capture summary (EAPOL pairs, PMKIDs, and warnings about unclean captures); by default only the best hash per handshake is kept — add `--all` to convert every message-pair variant. Feed the file to `hashcat -m 22000` or John.

### Extract cracker wordlists during conversion

Pull ESSIDs, identities and usernames out of the capture as wordlist material.

```
hcxpcapngtool -o capture.22000 -E essid-wordlist.txt -I identity-wordlist.txt -U username-wordlist.txt capture.pcapng
```

Outcome: in addition to the hash file, writes three list files — ESSIDs (non-ASCII characters auto-hexed), EAP identities and usernames harvested from the capture — usable directly as wordlist input for hashcat/JtR rules; `hcxeiutool` can further split them into digit/xdigit/character sub-lists.

### Inspect a hash file's contents

See what a converted hash file actually contains before attacking it.

```
hcxhashtool -i capture.22000 --info=stdout
```

Outcome: prints detailed statistics about the hash file — hash types, ESSIDs, message-pair statuses (challenge vs authorized), MACs — so you can judge capture quality; `--info-vendor=stdout` adds AP/client vendor breakdowns (run `hcxhashtool -d` once to download the IEEE OUI list they need).

### Filter a hash file down to the crackable subset

Drop junk hashes and keep only what you want to attack.

```
hcxhashtool -i capture.22000 -o authorized.22000 --authorized
```

Outcome: reads `capture.22000` and writes only matching hash lines to `authorized.22000`; `--authorized` keeps EAPOL pairs confirmed by the AP (M2M3/M3M4/M1M4), and the same command shape accepts `--essid=<ESSID>`, `--essid-regex=<regex>`, `--mac-ap=<MAC>`, `--vendor=<VENDOR>` and `--type=<digit>` to slice by any other dimension.

### Generate weak-PSK candidates for known-vendor defaults

Build a targeted wordlist from an ESSID using known weak password algorithms.

```
hcxpsktool -e MyWiFi -o psk-candidates.txt --netgear --weakpass
```

Outcome: writes candidate PSKs derived from the ESSID and enabled vendor rule sets (NETGEAR/Orbi, Spectrum/Charter, Tenda, ASUS RT-AC, 10-digit ISP defaults, full dates, WPS keys, ...) to `psk-candidates.txt`; use it with `hashcat -m 22000 --skip` style attacks against a 22000 hash file.

### Verify a PSK or PMK against a hash line

Confirm a recovered password actually satisfies a specific hash.

```
echo 'candidate-password' | hcxpmktool -l 'WPA*02*...' -p -
```

Outcome: checks the PSK (or a raw PMK) against the given hashcat-22000 hash line and reports whether the derived PMK/MIC matches — a quick offline sanity check before crediting a finding; `-p -` reads the secret from stdin so it never lands in shell history.

### Upload captures to wpa-sec

Submit capture files to the online WPA cracking-statistics service.

```
wlancap2wpasec -k <wpa-sec-user-key> capture.pcapng
```

Outcome: uploads the capture (gzip-compressed files accepted, multiple files and wildcards supported) to wpa-sec.stanev.org and reports the result; `-R` removes local files after a successful upload. Never merge different captures into one file — that destroys hash values.

### Resolve a MAC to its vendor

Identify who built an AP or client from its OUI.

```
whoismac -m aabbcc
```

Outcome: prints the vendor registered for the OUI `aabbcc` (the first three bytes of the MAC); run `whoismac -d` once to download the IEEE OUI reference list first — after that, `-p <22000 hashline>` resolves the vendor of any hash line directly.

No corpus evidence — derived from upstream documentation.
