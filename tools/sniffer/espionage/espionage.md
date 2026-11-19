---
name: "espionage"
categories:
  - blackarch-sniffer
version: "48.2d57edd"
homepage: "https://github.com/josh0xA/Espionage"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "josh0xA/Espionage"
github-stars: 252
github-pushed: "2024-08-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# espionage

> Python 3 traffic interceptor for Linux: live packet sniffing, HTTP/URL filtering and ARP-spoof MITM in one CLI.

## What it's for

Espionage (Josh Schiavone) is a Python 3 packet and traffic interceptor for Linux. It turns a named interface into a live monitor that shows packet direction, protocols and flags in a "normal" or more detailed "verbose" view, and it can write the whole capture to a .pcap file. Output can be narrowed to cleartext HTTP (port 80) or HTTPS (port 443) traffic, to visited URLs only, or extended to show raw HTTP/TCP data in byte order. It also bundles an ARP-spoofing mode: pass a single target host and its traffic is redirected through your machine — a man-in-the-middle position — while the sniffer keeps running. It needs root and an explicit `--iface`; Ctrl-C stops interception and finalizes the capture file. Scope is IPv4 TCP/UDP/ICMP/HTTP — it is not a protocol dissector suite and it does not decrypt TLS (upstream suggests pairing it with an SSL-stripping proxy for readable HTTPS).

## Typical use cases

### Sniff traffic and save a pcap

```
sudo espionage --normal --iface wlan0 -f capture_output.pcap
```

Outcome: prints a clean live feed of traffic on wlan0 (direction, protocol, flags); on Ctrl-C the capture is written to `capture_output.pcap` for analysis in Wireshark/tcpdump.

### Get a detailed view with raw HTTP bytes

```
sudo espionage --verbose --httpraw --iface wlan0
```

Outcome: verbose interception that additionally shows raw HTTP/TCP packet data in byte order for port 80 traffic.

### Intercept one target's traffic via ARP spoofing

```
sudo espionage --target 198.51.100.10 --iface wlan0
```

Outcome: ARP-spoofs 198.51.100.10 so its traffic routes through this machine while the live sniff displays it — the MITM position; the target's non-TLS data becomes visible.

### Harvest visited URLs

```
sudo espionage --iface wlan0 --urlonly
```

Outcome: prints only the URLs the monitored hosts visit over HTTP/HTTPS; upstream notes this works best combined with sslstrip, since raw TLS URLs are not visible.

### Restrict capture to cleartext HTTP

```
sudo espionage --iface wlan0 --onlyhttp
```

Outcome: displays only packets on port 80 (HTTP), reducing noise when hunting cleartext credentials or requests.

### Restrict capture to HTTPS metadata

```
sudo espionage --iface wlan0 --onlyhttpsecure
```

Outcome: displays only packets on port 443, showing connection metadata (hosts, timing) even though payloads stay encrypted.

*No corpus evidence — derived from upstream documentation.*
