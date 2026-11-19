---
name: "nbnspoof"
categories:
  - blackarch-spoof
version: "1.0"
homepage: "http://www.mcgrewsecurity.com/tools/nbnspoof/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "nomex/nbnspoof"
github-stars: 15
github-pushed: "2014-02-07"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nbnspoof

> NetBIOS Name Service spoofer — answers Windows NBNS broadcast queries with an attacker-chosen IP when DNS and WINS fail to resolve a name.

## What it's for

When a Windows machine cannot resolve a name via DNS or WINS, it falls back to broadcasting an NBNS (NetBIOS Name Service) query on the local network. nbnspoof (Robert Wesley McGrew, 2007) watches for those queries and immediately sends a spoofed positive response, steering the victim's connection to an address you choose — classic situations are a mistyped domain name or an unreachable DNS server. A regular expression decides which queried names get answered, and the reply's claimed IP and source MAC are fully attacker-controlled. It is a Python 2 + scapy script run as root; the packaged edition is the original with a maintained fork adding targeted-spoofing support.

## Typical use cases

### Redirect a specific name to your host

```bash
sudo nbnspoof -i eth0 -n 'www\.example\.com' -h 198.51.100.10 -m 00:11:22:33:44:55
```

Outcome: NBNS queries whose name matches the regex — here only `www.example.com` — receive forged responses claiming the name resolves to 198.51.100.10, sent with the given source MAC. Add `-v` to print every sniffed query and reply as they happen.

### Answer every NBNS query on the segment

```bash
sudo nbnspoof -i eth0 -n '.*' -h 198.51.100.10 -m 00:11:22:33:44:55 -v
```

Outcome: with the catch-all pattern, any broadcast name resolution attempt that reaches `eth0` is answered with 198.51.100.10, claiming every name on the network — useful when hunting for which legacy hosts still rely on NBNS at all.

*No corpus evidence — derived from upstream documentation.*
