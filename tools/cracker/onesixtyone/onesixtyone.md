---
name: "onesixtyone"
categories:
  - blackarch-cracker
version: "0.7"
homepage: "http://labs.portcullis.co.uk/application/onesixtyone/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "trailofbits/onesixtyone"
github-stars: 744
github-pushed: "2025-08-30"
github-archived: false
github-issues: 7
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# onesixtyone

> High-speed SNMP community-string scanner that bursts requests at many hosts instead of waiting per probe.

## What it's for

onesixtyone (designed by Alex Sotirov; original 0.7 release from the Portcullis era, now maintained under Trail of Bits) brute-forces SNMP community strings efficiently. Because SNMP runs over UDP and servers silently drop requests with a wrong community string, traditional scanners waste most of their time waiting for replies that never come; onesixtyone instead fires requests for `system.sysDescr.0` at every host as fast as the network tolerates (default 10 ms between packets, tunable with `-w`) and then logs the responses that do come back, Nmap-ping-sweep style. A response proves both a valid community string and reveals the device's system description — a classic SNMP reconnaissance foothold. Targets are IPv4 addresses or CIDR blocks on the command line or in a file; community strings come from a file or a small built-in default list.

## Typical use cases

### Sweep a subnet with one community string

```
onesixtyone 198.51.100.0/24 public
```

Outcome: every host in the /24 receives an SNMP request for `system.sysDescr.0` with community `public`; each answering host prints one line such as `198.51.100.10 [public] Cisco Systems WS-C5000`, confirming live SNMP devices and the working string.

### Scan host and community lists

Full cross product in one burst-scheduled run.

```
onesixtyone -c communities.txt -i hosts.txt
```

Outcome: every host in `hosts.txt` is probed with every community string in `communities.txt`; each valid host/community pair found is printed with the device's sysDescr. BlackArch ships a starter dictionary at `/usr/share/onesixtyone/dict.txt`.

### Throttled scan with a log file

Safer pacing on fragile networks.

```
onesixtyone -c communities.txt -i hosts.txt -o snmp-sweep.log -w 100
```

Outcome: packet spacing widens to 100 ms to avoid drops, and all findings are written to `snmp-sweep.log` in addition to stdout, giving a durable record of discovered strings and device descriptions.

*No corpus evidence — derived from upstream documentation.*
