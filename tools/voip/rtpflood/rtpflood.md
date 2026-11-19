---
name: "rtpflood"
categories:
  - voip
  - vulnerability
version: "1.0-1kali3"
homepage: "http://www.hackingvoip.com/sec_tools.html"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: other
meta-checked: "2026-11-09"
github-repo: "foreni-packages/rtpflood"
github-stars: 5
github-pushed: "2015-09-20"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtpflood

> Command-line RTP denial-of-service tool that blasts a fixed G.711 RTP packet at any device processing RTP.

## What it's for

Stressing the media-handling path of VoIP infrastructure. rtpflood is a small C program by Mark D. Collier and Mark O'Brien (SecureLogix, v1.0, July 2006), one of the SecureLogix attack tools published through the Endler/Collier books *Hacking Exposed VoIP* (hackingexposedvoip.com) and *Hacking VoIP* (hackingvoip.com). It sends one hardcoded 200-byte UDP/IP packet carrying a G.711 RTP payload as fast as the loop allows, with attacker-chosen sequence number, timestamp and SSRC/SSID values, so gateways, media servers and IP phones that track RTP sessions burn CPU and memory processing them. It has no options and no payload customization; for targeted RTP stream manipulation see the sibling tools [[rtpinsertsound]] and [[rtpmixsound]]. BlackArch packages this same tool under the name [[rtp-flood]].

## Typical use cases

### Flood an RTP endpoint

The command line is eight positional parameters: `rtpflood sourcename destinationname srcport destport numpackets seqno timestamp SSID`.

```
rtpflood target.example.com target.example.com 12345 16384 1000000 1 1 1
```

Outcome: prints `Will flood port 16384 from port 12345 1000000 times` and `Using sequence_number 1 timestamp 1 SSID 1`, then sends one million copies of the fixed G.711 RTP packet to the destination host and port; against an unhardened PBX or gateway this typically degrades call quality or stalls RTP processing — the measurable resilience gap the tool is used to demonstrate. Watch the target's media handling (and its logs) while it runs.

*No corpus evidence — derived from upstream documentation.*
