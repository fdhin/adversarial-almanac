---
name: "pentmenu"
categories:
  - blackarch-automation
version: "220.0cd7b58"
homepage: "https://github.com/GinjaChris/pentmenu"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-05-24"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "GinjaChris/pentmenu"
github-stars: 540
github-pushed: "2025-05-23"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pentmenu

> Bash menu for network recon and (mostly) denial-of-service testing: nmap/hping3-driven scans, a dozen packet-flood and resource-exhaustion attacks, plus netcat file exfiltration — aimed squarely at testing your own infrastructure.

## What it's for

Quick, menu-driven network testing without remembering per-tool syntax. Recon modules front nmap, ike-scan and hping3: show your own IPs, DNS/whois lookups, ping sweeps, quick TCP SYN scans (top 1000 ports), detailed scans with OS/banner detection, full UDP scans, uptime estimation and IPsec VPN discovery. The DOS side is the bulk of the tool — ICMP echo and Blacknurse floods, TCP SYN/ACK/RST/XMAS floods (with spoofable source IPs), UDP floods, OpenSSL handshake exhaustion (SSL DOS), Slowloris, IPsec Phase-1 floods, DNS NXDOMAIN floods, and a spoofed-source "distraction scan" — built from hping3/nping/netcat/openssl/stunnel rather than custom exploit code, and explicitly for authorized use against infrastructure you are stress-testing. The Extraction modules round it out with checksummed netcat file transfer and a listener. Everything is interactive menu choices; there is no flag-based CLI to script.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run a recon sweep from the menu

```
pentmenu
```

Outcome: opens the main menu; choose Recon and an entry such as Quick Scan, answer the prompts for target host/subnet, and the script drives nmap accordingly (the detailed scan touches all TCP ports, identifies OS and banners, and uses a Microsoft Edge User-Agent for its HTTP probes) — useful for getting nmap's common engagement profiles right without composing the flags yourself.

### Stress-test a service with a flood

From the menu, pick the DOS section and an attack such as Slowloris or TCP SYN flood:

```
pentmenu
```

Outcome: after prompting for target host/port and attack parameters, the tool launches the chosen flood via hping3 (falling back to nmap-nping if absent) — e.g. Slowloris slowly dribbles HTTP headers over netcat connections (SSL variant via stunnel), and the SYN flood lets you set or spoof the source address, which also enables reflection-style scenarios like LAND. Ctrl-C stops the flood; watch state-table and CPU behavior on the firewall/server you are testing.

### Move a file out with checksums

From the menu, use the Extraction section's Send File and Listener pair:

```
pentmenu
```

Outcome: on the receiving side pick Listener and a TCP/UDP port (received data lands in `./pentmenu.listener.out` or a file you name, with md5/sha512 checksums computed on TCP receipt); on the sending side pick Send File, choose the target and port, and the tool computes and displays md5/sha512 checksums before transferring — a quick ad-hoc exfiltration/testing channel whose integrity you can verify by comparing the hashes.
