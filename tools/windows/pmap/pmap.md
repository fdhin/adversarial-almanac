---
name: "pmap"
categories:
  - blackarch-windows
version: "1.10"
homepage: "http://www.hellfiresecurity.com/tools.htm"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pmap

> pMap — passive discovery, scanning and fingerprinting of link-local peers from the broadcast/multicast noise they generate (Hellfire Security).

## What it's for

pMap (Gregory Pickett, Hellfire Security; v1.10 2013, requires Administrator) discovers and profiles the machines on a local network segment without sending a single probe packet of its own: it listens to the traffic hosts emit anyway — ARP/broadcast chatter, mDNS, SSDP/UPnP, NBNS-style notifications — and turns that background noise into an inventory. For each discovered peer it can report device type, make and model (PCs, NAS, printers, phones, tablets, CCTV, DVRs), operating system and version, service versions and configuration, and open TCP/UDP ports (21, 22, 23, 80, 389, 445, 5353, 5060 are the ports it reasons about). Output is Nmap-like stand-alone text, or agent mode forwarding findings to a SYSLOG server; the distributed zip also ships a Metasploit post-exploitation module (`pmap.rb`) that captures via the Meterpreter channel and profiles the remote network. It is a Windows console tool for situational discovery of a LAN segment — not a general port scanner and not a sniffer you point at arbitrary capture files. The vendor site is offline (archived copies document the feature set); there is no canonical GitHub repository (same-named GitHub projects are unrelated to it).

## Typical use cases

### Passively profile the local segment

The zero-packet discovery run.

```
pmap.exe
```

Outcome: with no SYSLOG server given it runs in stand-alone mode, listens for the default 300 seconds, then prints a Nmap-style listing of discovered peers with what the noise revealed — device type/make/model, OS, services, and candidate open ports — without generating discovery traffic of its own.

### Restrict discovery to chosen ports and control the window

Shorten the profiling time and narrow the port reasoning.

```
pmap.exe -p:445,5353 -s:60
```

Outcome: profiles for 60 seconds instead of the default 300 (`-s:Seconds`), reasoning only about ports 445 and 5353 (`-p:Port` accepts the documented set 21, 22, 23, 80, 389, 445, 5353, 5060; defaults to all); `-t:Range` similarly narrows the subnet range examined (defaults to any/all subnets).

### Choose how much work it does

Pick discovery-only versus the fuller discover-and-scan pass.

```
pmap.exe -f:d
pmap.exe -f:a
```

Outcome: `-f:d` limits the run to discovery only, `-f:s` (the default) does discover-and-scan, and `-f:a` performs all functions including fingerprinting detail; add `-v` to also display the enumerated mDNS name records and notifications as they are collected.

### Run as a monitoring agent with SYSLOG output

Keep it running and ship findings to a central collector.

```
pmap.exe -a:198.51.100.10
```

Outcome: instead of stand-alone output, the tool starts a SYSLOG client toward 198.51.100.10 (`Address of the SYSLOG server`) and streams discovery events there — the agent mode intended for continuous segment monitoring.

*No corpus evidence — derived from upstream documentation and the packaged binary's usage output.*
