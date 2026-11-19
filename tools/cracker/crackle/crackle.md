---
name: "crackle"
categories:
  - blackarch-cracker
version: "111.d83b4b6"
homepage: "https://github.com/mikeryan/crackle/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2020-12-13"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "mikeryan/crackle"
github-stars: 980
github-pushed: "2021-08-26"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackle

> Cracks BLE (Bluetooth Smart) legacy pairing and decrypts captured BLE traffic.

## What it's for

crackle exploits a flaw in the Bluetooth Low Energy legacy pairing process: with Just Works and 6-digit PIN pairing the Temporary Key (TK) has at most 20 bits of entropy, so it can be guessed or brute-forced from a captured pairing exchange. From the TK it derives the Short Term Key and — if the devices re-exchange it, typically right after encryption starts — the Long Term Key (LTK), then decrypts the rest of the connection. Feed it a PCAP/PcapNG containing a BLE pairing conversation (captured for example with Ubertooth) and it either cracks the TK itself or decrypts using an LTK you already hold. It targets legacy pairing only; devices using LE Secure Connections are out of scope.

## Typical use cases

### Analyze a capture and crack the TK

Check whether a capture contains enough pairing traffic and recover the keys.

```
crackle -i pairing.pcap
```

Outcome: analyzes each connection in the file, reports the packet counts and the TK entropy (e.g. `Cracking with strategy 0, 20 bits of entropy`), prints the cracked TK and, if it was exchanged, the LTK — and reminds you to pass `-o` to write decrypted packets.

### Decrypt the capture into a new PCAP

Turn the cracked keys into readable traffic.

```
crackle -i pairing.pcap -o decrypted.pcap
```

Outcome: writes a new capture containing decrypted versions of all encrypted packets plus the originally unencrypted ones; note that CRCs of decrypted packets are not recalculated.

### Decrypt with an already-known LTK

Skip TK recovery when the LTK was recovered earlier.

```
crackle -i encrypted.pcap -o decrypted.pcap -l 81b06facd90fe7a6e9bbd9cee59736a7
```

Outcome: uses the supplied 128-bit hex LTK (most-significant octet first, no separators) to decrypt the connection directly, producing the same style of output PCAP as TK-cracking mode.

*No corpus evidence — derived from upstream documentation.*
