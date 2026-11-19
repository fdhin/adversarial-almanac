---
name: "redpoint"
categories:
  - blackarch-misc
version: "123.23ef36b"
homepage: "https://github.com/digitalbond/Redpoint"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "digitalbond/Redpoint"
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 473
github-pushed: "2020-03-10"
github-archived: false
github-issues: 7
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# redpoint

> Digital Bond's collection of nmap NSE scripts that discover and enumerate ICS/SCADA devices speaking BACnet, EtherNet/IP, Siemens S7, DNP3, Omron, Modicon, CoDeSys, Niagara Fox, PC Worx and ProConOS.

## What it's for

RedPoint is Digital Bond's ICS enumeration project: a set of nmap NSE scripts that use legitimate protocol commands — nothing here exploits or deliberately crashes anything — to identify industrial devices and harvest details useful for further assessment: vendor and model, firmware and software revisions, object identifiers, device names and network tables. The scripts are plain `.nse` files with no standalone CLI; they are run through nmap, and the BlackArch package installs them under `/usr/share/nmap/scripts/redpoint/`. ICS devices and applications are frequently fragile and can crash or misbehave on any unexpected traffic, so only point them at systems you are authorized to test. Many scripts pair a fixed ICS port with a UDP or TCP scan to match the protocol's transport.

## Typical use cases

### Detect and enumerate BACnet devices

Find building-automation devices over UDP and pull their identity.

```
nmap -sU -p 47808 --script /usr/share/nmap/scripts/redpoint/BACnet-discover-enumerate.nse target
```

Outcome: reports whether the target speaks BACnet and enumerates vendor ID/name, object identifier, firmware and model; add `--script-args full=yes` to also pull the broadcast (BDT) and foreign-device (FDT) tables.

### Enumerate Siemens SIMATIC S7 PLCs

Identify S7 controllers on the classic Profinet port.

```
nmap -p 102 --script /usr/share/nmap/scripts/redpoint/s7-enumerate.nse target
```

Outcome: sends S7 identification requests and prints the PLC's identity and catalog details if the target responds.

### Identify EtherNet/IP devices

Enumerate Rockwell Automation and other CIP-based devices.

```
nmap -p 44818 --script /usr/share/nmap/scripts/redpoint/enip-enumerate.nse target
```

Outcome: lists the ENIP/CIP device identity (vendor, product name, serial, revision) of any responding EtherNet/IP adapter.

### Pull Schneider Modicon PLC information

Combine the script with version detection on Modbus TCP.

```
nmap -p 502 -sV --script /usr/share/nmap/scripts/redpoint/modicon-info.nse target
```

Outcome: reports the Modicon PLC's identity alongside nmap's service banner for port 502.

### Enumerate Omron PLCs over UDP

Query Omron controllers on their FINS port.

```
nmap -sU -p 9600 --script /usr/share/nmap/scripts/redpoint/omronudp-info.nse target
```

Outcome: sends a FINS request over UDP and prints the controller model and version; `omrontcp-info.nse` is the TCP equivalent.

*No corpus evidence — derived from upstream documentation.*
