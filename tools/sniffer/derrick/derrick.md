---
name: "derrick"
categories:
  - sniffer
version: "0.3"
homepage: "https://github.com/rieck/derrick"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: autotools
license: custom
meta-checked: "2026-11-09"
github-repo: "rieck/derrick"
github-stars: 35
github-pushed: "2019-03-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# derrick

> Records TCP and UDP application payloads as line-based ASCII logs that grep, sed, awk and netcat handle directly.

## What it's for

derrick (Konrad Rieck) is a network stream recorder specialized for application-layer communication. Instead of a pcap it writes one line per payload — `<TIME> <FLAG> <SRC> <DST> <PAYLOAD>` — with TCP streams reassembled via libnids (begin/end marked `T+`/`T-`, payload lines `T`, UDP lines `U`) and non-printable bytes URI-escaped as `%XX`. The point of the format is processability: standard UNIX text tools apply immediately, and recorded sessions can be replayed to a server with netcat. Lower-layer details (headers, handshakes) are deliberately omitted. It is weaker than a full analyzer like Wireshark and has no protocol dissectors — it is for monitoring, grepping and replaying plaintext protocol conversations.

## Typical use cases

### Record all streams on an interface

Watch application-layer chatter as readable lines.

```
sudo derrick -i eth0
```

Outcome: every TCP payload (reassembled into streams, with `T+`/`T-` marking session open and close) and each UDP payload is written to stdout as one escaped line with timestamps, endpoints and direction — e.g. HTTP GET requests appear as plain readable text.

### Record only matching traffic

Narrow the recording to one service or host.

```
sudo derrick -i eth0 -f "port 80"
```

Outcome: only packets matching the tcpdump-style filter expression are recorded, so the log contains just the protocol of interest (here HTTP) instead of all traffic.

### Extract sessions from a pcap dump

Apply the same line format offline.

```
derrick -r capture.pcap
```

Outcome: derrick reads the pcap dump and emits the identical one-line-per-payload format for the sessions it contains, without touching a live interface.

### Run as a long-term background recorder

Log continuously with rotation and bounded stream length.

```
sudo derrick -i eth0 -l traffic.log -t 1000000 -b 8192
```

Outcome: output goes to the compressed log file `traffic.log` instead of stdout, rotated after each million lines (`-t`), with only the first 8192 bytes of every TCP stream recorded (`-b`) so large transfers cannot flood the log.

### Merge split protocol messages

Rejoin messages that span several TCP segments.

```
sudo derrick -i eth0 -m
```

Outcome: consecutive TCP payloads in the same direction are merged into one line before logging — protocols that split a request across segments (common with long HTTP requests) appear as single, complete messages.

*No corpus evidence — derived from upstream documentation.*
