---
name: "fs-nyarl"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://www.fulgursecurity.com/en/content/fs-nyarl"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-06"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fs-nyarl

> Fulgur Security's interactive network-takeover console: one menu drives ARP poisoning, sniffing, SSL hijacking and client-side attacks, then automatically distils the captured traffic into passwords, sessions, files and an HTML report.

*No corpus evidence — derived from upstream documentation.*

## What it's for

FS-NyarL (named for Lovecraft's Crawling Chaos, by Alberto Fontanella of Fulgur Security, 2013) is a penetration-testing orchestrator: instead of memorizing a dozen MITM tools, you get a single interactive console that coordinates them — ettercap/arpspoof for ARP poisoning, mitmproxy/sslstrip/sslsplit for SSL interception, tcpdump for capture, beef, metasploit and SET for client-side work, chaosreader/xplico/foremost for afterwards. From the console you run MITM with or without poisoning, plain sniffing, full SSL/TLS hijacking, HTTP session hijacking (take and replay cookies), browser takeover and autopwn plus an evil Java applet via filter injection into the data stream, DNS spoofing, and port scanning. The payoff is the forensic layer: passwords stream in live, hosts enumerate with IP/MAC/OS as they speak, and the run produces a pcap of the whole stream, extracted session flows, files, URLs, cookies and images, ending in an automatically generated HTTP report. The vendor's own caveat stands: use at your own risk — pointed at a network segment it is a full interception apparatus.

## Typical use cases

### Launch the console against a network segment

```
sudo fs-nyarl
```

Outcome: opens the FS-NyarL interactive console (root is required for poisoning and capture). Pick the interface, target and attack from the menus; as traffic flows you see passwords and enumerated hosts appear in real time, evidence accumulates in pcap and session files, and on completion the tool writes its HTML report of extracted credentials, sessions, files and hosts for review.
