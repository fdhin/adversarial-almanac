---
name: "xplico"
categories:
  - blackarch-forensic
version: "1.2.2"
homepage: "https://github.com/xplico/xplico/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-10-01"
build-system: perl
license: CC-BY-3.0, GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "xplico/xplico"
github-stars: 203
github-pushed: "2020-08-28"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# xplico

> Network-forensics decoder that turns packet captures into per-protocol evidence: web sessions, mail, files, VoIP and a Google Earth track.

## What it's for

Xplico (the "Internet Traffic Decoder", a Network Forensic Analysis Tool) reconstructs application-level content from captures: HTTP sessions and the files they transferred, POP/SMTP/IMAP e-mail, DNS, VoIP flows and more are decoded out of a pcap into per-protocol output directories, together with a KML file that plots the capture's data geographically in Google Earth. It processes a single capture file, a directory of captures, or a live interface in realtime, and it also ships a web interface for case-driven work — console mode, covered here, is the scriptable half. It is a decoder, not an IDS: it recognizes protocols and extracts their content, and per-interface options are listed by `xplico -h -m pcap` (or `-m rltm`). Upstream development has been dormant since 2020.

## Typical use cases

### Decode a single capture

Turn one pcap into reviewable content.

```
xplico -m pcap -f capture.pcap
```

Outcome: decodes the capture and writes reconstructed content per protocol under `tmp/xplico/` (`xdecode/ip/http`, `xdecode/ip/pop`, `xdecode/ip/smtp`, ...), with the KML file alongside — browse the directories to review recovered pages, mails and transferred files.

### Decode a directory of captures

Process a multi-file acquisition in one run.

```
xplico -m pcap -d /mnt/case/pcaps/
```

Outcome: every pcap in the directory is decoded in sequence into the same per-protocol output tree, consolidating the acquisition.

### Decode live traffic

Capture and decode from an interface as it happens.

```
xplico -m rltm -i eth0
```

Outcome: captures and decodes traffic from the interface in realtime until interrupted with Ctrl-C; decoded content accumulates in the same `xdecode/` tree and the KML file refreshes every 30 seconds.

*No corpus evidence — derived from upstream documentation.*
