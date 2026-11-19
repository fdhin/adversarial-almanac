---
name: "hb-honeypot"
categories:
  - identify
version: "0.1.1-1kali5"
homepage: "https://packetstormsecurity.com/files/126068/Heartbleed-Honeypot-Script.html"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali, nixpkgs]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/hb-honeypot"
github-stars: 0
github-pushed: "2024-02-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hb-honeypot

> Perl honeypot that answers Heartbleed probes — and rickrolls the scanner.

## What it's for

hb-honeypot is the classic Heartbleed honeypot script: it listens on TCP
port 443 and answers TLS heartbeat requests with completely bogus data —
except when it recognizes the byte pattern of a genuine Heartbleed
(CVE-2014-0160) exploit attempt, to which it responds with a hex dump that
rickrolls the scanner. Every suspected scanner's source IP is printed to the
console, giving a live feed of who is probing for a (fake) vulnerable TLS
service. Deploy it on a decoy host to detect Heartbleed scanning campaigns
in your address space; it serves no real content and is not a TLS stack —
its only job is to be found and probed. Run it as root (privileged port),
and note it takes no options: port and behaviour are fixed.

## Typical use cases

### Run the Heartbleed decoy

```
sudo hb-honeypot
```

Outcome: binds TCP 443; Heartbleed-shaped heartbeats are answered with the
rickroll hex dump and each scanner's source IP is printed to the console —
redirect the output to a file (or run under a service manager) to keep a
probe log.

*No corpus evidence — derived from upstream documentation (PacketStorm
release mirrored in the linked repository; Kali ships the script as the
`hb-honeypot` command).*
