---
name: "hexinject"
categories:
  - blackarch-sniffer
version: "1.6"
homepage: "http://hexinject.sourceforge.net"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2017-06-03"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "ParrotSec/hexinject"
github-stars: 25
github-pushed: "2017-07-26"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hexinject

> Hexstring-based packet sniffer and injector built for shell pipelines, with prettypacket and hex2raw helpers.

## What it's for

HexInject (Emanuele Acri) is a small C suite built on libpcap that gives the command line raw network access. The main `hexinject` binary either sniffs packets and prints them as hexstrings, or reads hexstrings from standard input and injects them as raw frames on an interface. Two companions round it out: `prettypacket` disassembles raw packets from stdin into named fields and can emit example packets for common protocols, and `hex2raw` converts between hexstrings and raw bytes. Because every stage is line-oriented text, the tools compose with ordinary shell utilities into sniff → edit → re-inject pipelines — capture a packet, tweak a field with sed, and put the modified frame back on the wire. It works on any pcap-supported interface (with an experimental monitor-mode switch for wireless), applies automatic checksum and size fields on injection unless disabled, and does not understand higher-level protocols itself — you edit bytes, not parsed objects.

## Typical use cases

### Sniff live traffic as editable hexstrings

```
sudo hexinject -s -i eth0 -f "tcp port 80"
```

Outcome: prints every matching packet on eth0 as a hexstring line; redirect to a file to capture a template for later modification and reinjection.

### List available capture devices

```
hexinject -I
```

Outcome: lists the network devices pcap can open, with their addresses — use one as the `-i` value.

### Re-inject a known-good example packet

```
prettypacket -d arp | sudo hexinject -p -i eth0
```

Outcome: `prettypacket -d arp` dumps a valid example ARP packet as a hexstring; hexinject inject mode (`-p`) sends it as a raw frame on eth0.

### Disassemble a captured packet into fields

```
sudo hexinject -s -r -i eth0 -c 1 | prettypacket
```

Outcome: captures one packet in raw mode (`-r`) and prints it broken into named header fields — the quick way to see which hex bytes correspond to which header values.

### Learn a protocol's header layout

```
prettypacket -x tcp
```

Outcome: prints a field-by-field example TCP packet (types include tcp, udp, icmp, igmp, arp, stp, ipv6_tcp, ipv6_icmp, ipv6_in_ipv4) so you know the structure before crafting your own.

### Convert data between hexstring and raw bytes

```
echo Hello | hex2raw -r
```

Outcome: prints the hexstring encoding of the input bytes; run without `-r` to convert a hexstring on stdin back into raw bytes — the bridge between text tools and raw sinks.

### Re-read a saved capture through hexinject

```
hexinject -s -F capture.pcap
```

Outcome: replays the pcap file through the same hexstring output as a live sniff (sniff mode only), letting you mine or modify recorded traffic offline.

*No corpus evidence — derived from upstream documentation.*
