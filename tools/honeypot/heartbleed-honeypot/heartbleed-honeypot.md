---
name: "heartbleed-honeypot"
categories:
  - blackarch-honeypot
version: "0.1"
homepage: "http://packetstormsecurity.com/files/126068/hb_honeypot.pl.txt"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/hb-honeypot"
github-stars: 0
github-pushed: "2024-02-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# heartbleed-honeypot

> One-file Perl tripwire that answers SSL heartbeat requests on port 443 and reports which sources are scanning for Heartbleed.

## What it's for

`hb_honeypot.pl` (published by glitchwrks on 8 April 2014, days after CVE-2014-0160 hit) is a quick decoy for the Heartbleed mass-scan wave. It listens on TCP/443, answers SSL traffic with deliberately bogus heartbeat responses, and when it recognizes the start of the byte pattern used by Jared Stafford's public Heartbleed demo script, prints the source IP of the scanning host to the console. That is the whole tool: it takes no options, the port is hardcoded to 443 (so it must run as root), and it collects nothing beyond scan notifications. Use it as a minimal early-warning indicator of Heartbleed scanning against an address you control; it does not test for or exploit the vulnerability itself.

## Typical use cases

### Trip a warning on Heartbleed mass scans

Expose the decoy on a host you control and watch the console.

```
sudo heartbleed-honeypot
```

Outcome: the script binds TCP/443, keeps answering heartbeat traffic with junk responses, and prints the address of each connection whose payload matches the pattern of the public Heartbleed demo to stdout — a live feed of who is sweeping your address space for CVE-2014-0160.

*No corpus evidence — derived from upstream documentation.*

GitHub: third-party mirror of the PacketStorm upstream (`hb_honeypot.pl`, identical apart from a filename-comment tweak).
