---
name: "dedected"
gentoo-category: "net-wireless"
version: "9999"
homepage: "https://dedected.org"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "LucaBongiorni/dedected"
github-stars: 16
github-pushed: "2015-09-03"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dedected

> The DECT research framework: a Linux driver for com-on-air PCMCIA DECT cards plus sniffer tools that capture base-station and handset traffic to pcap.

## What it's for

Dedected is the classic open-source DECT (Digital Enhanced Cordless Telecommunications) security research framework. Its core, com-on-air_cs-linux, is a driver for the Dosch & Amand com-on-air PCMCIA DECT cards (exposing the card at /dev/coa) with userspace tools built alongside: dect_cli for interactive scanning of base stations (fixed parts) and handsets (portable parts), coa_syncsniff for locking onto a station's RFPI and dumping traffic to pcap, and pcap2cchan/pcapstein for extracting C-channel and B-field data from captures. Sibling directories add Kismet DECT plugins and a Metasploit module. The framework dates from the 2007-2009 research wave (most DECT bases then shipped no or broken authentication); modern bases usually encrypt, so its practical role today is research and lab capture. It requires com-on-air-class hardware — it is not a generic SDR DECT sniffer.

## Typical use cases

### Scan interactively for DECT base stations and handsets

```
dect_cli
```

Outcome: opens the interactive console (type `help` when lost); `fpscan` sweeps for fixed parts and `ppscan` for portable parts, listing stations with RFPI, channel and RSSI; known stations persist in stations.rc between runs, and `dump` writes a per-station pcap (`dump_<type>_RFPI_<bytes>.pcap`).

### Lock onto a station and capture its traffic

```
coa_syncsniff 4 sync.pcap "11 22 33 44 55"
```

Outcome: sets the card to sync-sniff mode on channel 4, locks to the given RFPI (five space-separated hex bytes) and appends the station's DECT frames to the pcap file for analysis in Wireshark.

### Mine captured pcaps for call and control data

```
pcapstein sync.pcap
```

Outcome: dumps all B-fields — the payload/call data frames — found in the capture to the console for offline inspection; pcap2cchan extracts the C-channel (control) information from the same file the same way.

*No corpus evidence — derived from upstream documentation.*
