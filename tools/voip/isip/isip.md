---
name: "isip"
categories:
  - blackarch-voip
version: "2.fad1f10"
homepage: "https://github.com/halitalptekin/isip"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "halitalptekin/isip"
github-stars: 79
github-pushed: "2015-12-17"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# isip

> Interactive Python console for crafting, inspecting, capturing and sending SIP packets.

## What it's for

isip (Halit Alptekin) is an interactive SIP toolkit built on scapy — a cmd-style console where SIP messages are first-class objects. From the `packet` sub-loop you create SIP messages, edit any IP/UDP/SIP field (including randomised values for fuzz-style stress), load messages from pcaps or text files, save them back to pcap, hand them to Wireshark, and send them single-shot or in bursts. The packaged version is Python 2 and the upstream repository is archived, so it targets classic UDP SIP; the README also lists sniffing, man-in-the-middle and DoS simulation among its capabilities.

## Typical use cases

### Craft and send a SIP message

Enter the console, build a request field by field, and transmit it.

```
isip
isip:main> packet
isip:packet> new r1
isip:packet> set r1 ip dst 198.51.100.10
isip:packet> set r1 sip method OPTIONS
isip:packet> send r1 1
```

Outcome: each command prints feedback in the console; `send r1 1` transmits the crafted OPTIONS request once to 198.51.100.10:5060 — raise the count to send it repeatedly.

### Randomise header values for malformed-traffic bursts

Fill header fields and body with random data, then fire a burst.

```
isip:packet> set r1 sip headers.from random-headers-from
isip:packet> set r1 sip headers.contact random-headers-contact
isip:packet> set r1 sip body random-data 50
isip:packet> send r1 150
```

Outcome: 150 copies are sent, each with different randomised From/Contact values and a 50-byte random body — a quick stream of malformed SIP to stress a lab target.

### Load and replay packets from a capture

Pull SIP messages out of a pcap, inspect and re-send them.

```
isip:packet> load capture.pcap r1
isip:packet> show r1 sip
isip:packet> send r1 1
```

Outcome: the SIP messages found in capture.pcap load as an editable packet series (r1.0, r1.1, ...), `show` prints their fields, and `send` re-transmits them; `save r2 out.pcap` writes the series back to a pcap and `wireshark r1` opens it in Wireshark for inspection.

*No corpus evidence — derived from upstream documentation.*
