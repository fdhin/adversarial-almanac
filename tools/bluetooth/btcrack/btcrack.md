---
name: "btcrack"
categories:
  - blackarch-bluetooth
version: "1.1"
homepage: "http://www.nruns.com/_en/security_tools_btcrack.php"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "mikeryan/btcrack"
github-stars: 53
github-pushed: "2014-05-12"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# btcrack

> Bruteforces the PIN (passkey) and resulting link key from a captured classic-Bluetooth pairing exchange.

## What it's for

btcrack (BTCrack, by Eric Sesterhen and Thierry Zoller of n.runs; Linux port maintained on GitHub by Mike Ryan) takes an offline capture of a pairing exchange between two devices and bruteforces the short PIN that produced it, recovering the link key — after which the captured traffic (and, while the key stays valid, future connections between the pair) can be decrypted. You supply the maximum PIN length to try, the two device addresses, and a CSV of the pairing packets captured with a sniffer; it prints the recovered PIN and link key. This attacks legacy BR/EDR pairing only — Bluetooth 4.2+ Secure Connections (ECDH) pairing is not vulnerable. The distro package ships the upstream Windows build under `/usr/share/windows/btcrack`, invoked identically under Wine.

## Typical use cases

### Crack a captured pairing exchange

Recover the PIN and link key from a sniffed pairing capture.

```
btcrack 8 00:11:22:33:44:55 00:66:77:88:99:AA pairing_capture.csv
```

Outcome: bruteforces PINs up to 8 characters for the address pair whose exchange is in the CSV and prints the matching PIN and link key when found. A working self-test ships with the source: `./btcrack 1 00:11:9F:C4:F3:AE 00:60:57:1A:6B:F1 Pin_654321.csv`.

*No corpus evidence — derived from upstream documentation.*
