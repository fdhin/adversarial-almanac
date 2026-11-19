---
name: "ipdecap"
categories:
  - blackarch-networking
version: "96.45d2a7d"
homepage: "https://loicpefferkorn.net/ipdecap/"
packaged-by: [blackarch]
blackarch-builddate: "2023-02-28"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lpefferkorn/ipdecap"
github-stars: 35
github-pushed: "2018-10-14"
github-archived: false
github-issues: 4
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipdecap

> Strips GRE, IPIP, 6in4, ESP (IPsec) and IEEE 802.1Q encapsulation out of pcap files, decrypting ESP when given the security associations.

## What it's for

ipdecap reads a pcap capture, removes the encapsulation header from every supported packet, and writes the inner (decapsulated) packets to a new pcap that ordinary analyzers can read. Supported encapsulations: GRE and IPIP (IPv4), 6in4 (IPv6-in-IPv4), ESP/IPsec (IPv4) and IEEE 802.1Q VLAN tags. Encrypted ESP traffic is only decoded when you supply a configuration file describing the security associations for both flow directions (algorithm, hex key, hex SPI); AH integrity-check values are not verified and ESP transport mode is unsupported. Written by Loïc Pefferkorn, GPLv3; documentation lives at https://loicpefferkorn.net/ipdecap/.

## Typical use cases

### Remove GRE/IPIP/6in4 tunneling from a capture

Analyze inner packets in any standard tool.

```
ipdecap -i tunneled.cap -o decapsulated.cap -v
```

Outcome: writes `decapsulated.cap` containing the inner packets of every GRE/IPIP/6in4 packet found in the input; `-v` prints per-packet details (recognized protocol, decryption result) as it runs.

### Decapsulate only matching traffic

Limit processing with a BPF filter.

```
ipdecap -i capture.cap -o out.cap -f "src 198.51.100.10 and dst 198.51.100.11"
```

Outcome: only packets between the two addresses are processed from the input file; everything else is skipped.

### Decrypt ESP with known security associations

Decode IPsec tunnel traffic you hold the keys for.

```
ipdecap -i esp.cap -o out.cap -c esp.conf
```

with `esp.conf` holding one line per direction in the form `<hostA> <hostB> <enc alg> <auth alg> <key (hex)> <SPI (hex)>`:

```
198.51.100.10 198.51.100.11 3des-cbc hmac_sha1-96 0x785778a2d4b0f36bf17a8c55d9b6cea7abcdef43f9d0d8b0 0x04022464
198.51.100.11 198.51.100.10 3des-cbc hmac_sha1-96 0xdeadbeeffff23a964457224d4a05121247bdbc8f0dda23fc 0x02250089
```

Outcome: ESP packets matching a configured flow are decrypted and written as plain inner packets to `out.cap`; run `ipdecap -l` to list every supported encryption and authentication algorithm (the config can be generated from `setkey -Da` output with the bundled `sadb2conf.awk`).

*No corpus evidence — derived from upstream documentation.*
