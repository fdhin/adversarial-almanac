---
name: "ohrwurm"
categories:
  - blackarch-fuzzer
version: "1.7a1182a"
homepage: "http://mazzoo.de/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2019-08-13"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "mazzoo/ohrwurm"
github-stars: 17
github-pushed: "2010-09-30"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ohrwurm

> Fuzz the RTP media stream of a live VoIP call by forwarding its packets with injected bit errors.

## What it's for

ohrwurm (Matthias Wenzel, 2006; German for "earwig") is a small RTP fuzzer for VoIP robustness testing on a LAN. It watches local traffic for the SIP signaling between two IPv4 endpoints, identifies the RTP media stream of an ongoing audio call, and then forwards the RTP packets of that call while flipping random bits at a chosen error ratio — the finding is whatever the endpoints then do (crash, silently stop sending media, renegotiate the codec mid-call). Because it must sit in the packets' path, it is normally combined with ARP spoofing (dsniff's arpspoof) to pull the call's traffic through the fuzzing host; alternatively, fixed RTP port numbers can be supplied to skip SIP discovery and fuzz any RTP pair regardless of how signaling is done. Its scope is deliberately narrow — IPv4 LAN calls, RTP payload bit-flipping, no RTCP fuzzing — and running it with no arguments prints the usage summary.

## Typical use cases

### Fuzz the call between two phones

The documented on-path (ARP-spoofed) workflow.

```
arpspoof 198.51.100.10
arpspoof 198.51.100.20
ohrwurm -a 198.51.100.10 -b 198.51.100.20
```

Outcome: with both ARP-spoofing redirects active, the two phones' traffic flows through this host; once a SIP call starts between the two addresses, ohrwurm locks onto the negotiated RTP stream and forwards it with random bits flipped at the default 1.23% error ratio — watch the endpoints for crashes, renegotiation or a stalled stream, then Ctrl-C to stop.

### Fuzz a fixed RTP port pair

Skip SIP sniffing entirely.

```
ohrwurm -a 198.51.100.10 -b 198.51.100.20 -A 5004 -B 5004 -i eth0
```

Outcome: fuzzes RTP traffic directly between the two given ports without looking for SIP on 5060 — per the built-in usage note, "any RTP can be fuzzed" this way, which also covers SIP on nonstandard ports or pre-negotiated media streams.

### Make a fuzzing run reproducible

Pin the randomness so a crash can be replayed.

```
ohrwurm -a 198.51.100.10 -b 198.51.100.20 -s 1234 -e 2.5
```

Outcome: seeds the PRNG (`-s`) and fixes the bit error ratio at 2.5% (`-e`, default 1.23), so the exact sequence of corrupted packets repeats on every run with the same seed; without `-s` the seed comes from /dev/urandom and no two runs match.

*No corpus evidence — derived from upstream documentation.*
