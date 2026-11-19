---
name: "sipffer"
categories:
  - blackarch-sniffer
version: "29.efc3ff1"
homepage: "https://github.com/xenomuta/SIPffer"
packaged-by: [blackarch]
blackarch-builddate: "2017-08-18"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "xenomuta/SIPffer"
github-stars: 14
github-pushed: "2020-04-16"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipffer

> Command-line SIP sniffer for quick troubleshooting of VoIP signaling traffic.

## What it's for

SIPffer (SIP + sniffer, by xenomuta) is a lightweight C tool for diagnosing SIP call flows through proxies, SBCs, and PBXs in high-traffic environments where Wireshark is too heavy to install or run. It filters live or captured SIP traffic with PCRE regular expressions, additional BPF filters, SIP methods, response codes, or a single header, and it can follow one whole dialog by Call-ID. It decodes SIP signaling only — it does not reassemble or play RTP media. The default target is UDP port 5060, and on Linux it sniffs all interfaces unless told otherwise.

## Typical use cases

### Sniff SIP matching a regular expression

Watch live SIP signaling and show only messages matching a PCRE.

```
sipffer -i eth0 'user@example\.com'
```

Outcome: prints matching SIP messages (default UDP/5060) live as they arrive on the interface; Ctrl-C stops the capture.

### Filter by SIP method or response code

Narrow a noisy capture to particular methods or response codes.

```
sipffer -i eth0 -m INVITE
sipffer -i eth0 -r 404
```

Outcome: shows only INVITE requests (first form) or only 404 responses (second form), letting you count failed registrations or calls without reading every packet.

### Follow a complete call dialog

Track one session end-to-end by its Call-ID.

```
sipffer -i eth0 -s 'user@example\.com'
```

Outcome: after the first packet matches the regex, only messages sharing that Call-ID are printed, giving a single dialog's message sequence.

### Scope the match to one SIP header

Restrict the regular expression to a single header such as From or Contact.

```
sipffer -i eth0 -c From 'user@example\.com'
```

Outcome: only messages whose From header matches are shown; matches in other headers or the body are ignored.

### Analyze SIP on a non-standard port

Capture signaling that does not run on the default 5060.

```
sipffer -i eth0 -p 5061 -f 'host 198.51.100.10'
```

Outcome: watches port 5061 instead of 5060, additionally constrained by the tcpdump-style BPF filter, so only the selected host's signaling on that port is printed.

### Read a saved capture offline

Run the same filters against an existing pcap instead of a live interface.

```
sipffer -a signaling.pcap -m REGISTER
```

Outcome: processes the capture file from disk, prints matching messages, and exits — useful for post-mortem analysis of a tcpdump taken on a server.

*No corpus evidence — derived from upstream documentation.*
