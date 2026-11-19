---
name: "cdpsnarf"
categories:
  - blackarch-sniffer
version: "0.1.6"
homepage: "https://github.com/Zapotek/cdpsnarf"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "Zapotek/cdpsnarf"
github-stars: 42
github-pushed: "2010-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cdpsnarf

> Passive Cisco Discovery Protocol sniffer that reconstructs device identity, ports and capabilities from CDP advertisements on a segment.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CDPSnarf (Tasos "Zapotek" Laskos, GPL-2, 2010) extracts everything a `show cdp neighbors detail` would tell you — and more — purely by listening: device ID, addresses (IPv4/IPv6), port ID, capabilities, software version, platform, duplex, TTL and checksum, plus the time intervals between CDP advertisements. During internal pentests CDP mappings reveal neighbor infrastructure without touching it. It is passive only — it neither sends CDP nor spoofs it — and can read from a live interface or a saved pcap and archive what it sees. The tool is a 2010-era C program that still builds against libpcap; it speaks CDP only, so use LLDP/other sniffers for non-Cisco discovery traffic.

## Typical use cases

### Sniff CDP live on a segment

```
sudo cdpsnarf -i eth0
```

Outcome: prints one structured block per CDP advertisement heard on eth0 — device ID, addresses, port, capabilities, version, platform, duplex, TTL and advertisement intervals — building the neighbor map as Cisco devices announce themselves; `cdpsnarf -h` lists all options.

### Extract CDP from a saved capture

```
cdpsnarf -r capture.pcap
```

Outcome: applies the same parsing to an offline pcap (`pcap_open_offline`) with no interface or privileges needed — useful for captures taken elsewhere or stored evidence.

### Sniff while archiving raw CDP packets

```
sudo cdpsnarf -i eth0 -w cdp-dump.pcap -d
```

Outcome: in addition to the parsed output, every matching packet is written to `cdp-dump.pcap` (`-w`) for re-analysis, while `-d` enables the debug view of the parsing for troubleshooting captures the tool does not fully decode.
