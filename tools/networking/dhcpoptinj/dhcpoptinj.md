---
name: "dhcpoptinj"
categories:
  - blackarch-networking
version: "123.58a12c6"
homepage: "https://github.com/misje/dhcpoptinj"
packaged-by: [blackarch]
blackarch-builddate: "2019-08-07"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
github-repo: "misje/dhcpoptinj"
github-stars: 42
github-pushed: "2020-07-07"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dhcpoptinj

> Injects arbitrary DHCP options into BOOTP/DHCP packets as they transit a Linux host.

## What it's for

dhcpoptinj sits between DHCP clients and servers and quietly adds (or replaces) options in passing DHCP requests — useful when you control neither endpoint: intercepting requests on a bridge to inject option 82 (relay agent information), overriding hostnames, or experimenting with exotic or malformed options. It pulls packets from an iptables NFQUEUE rule, verifies each is a BOOTP/DHCP packet, appends the configured options, recalculates the IPv4 header checksum (the UDP checksum is zeroed for simplicity), and reinjects the packet. It needs the CAP_NET_ADMIN capability — grant it to the binary with setcap instead of running as root. It is purely a mangler, not a DHCP server, relay, or proxy.

## Typical use cases

### Intercept bridge DHCP traffic and add option 82

Queue DHCP requests arriving on one side of a bridge, then inject the relay agent information option.

```
sudo iptables -t mangle -A PREROUTING -m physdev --physdev-in eth0 -p udp --dport 67 -j NFQUEUE --queue-num 42
sudo dhcpoptinj -f -d -q 42 -o'52 01 04 46 6A 61 73'
```

Outcome: dhcpoptinj runs in the foreground (-f) with debug output (-d) and reports "Initialisation completed. Waiting for packets to mangle on queue 42". Each passing DHCP request is logged option-by-option and forwarded with the new option 52 payload ("Fjas") appended just before the END option; a capture on the far side of the bridge shows the injected option, a capture on the incoming interface does not.

### Replace existing options instead of appending

Swap the hostname and requested-address options a client sends with your own values.

```
sudo dhcpoptinj -f -d -q 42 -r -o'0C 66 6A 61 73 65 68 6F 73 74' -o 320A141E28
```

Outcome: with -r the log marks every original option "(removing)" instead of "(copying)"; the two -o options are injected — option 12 (Hostname) "fjasehost" and option 50 (Address Request) 0x0A141E28 — followed by the END option and BOOTP padding. The -o argument is a hex string of option code then payload (the length byte is computed automatically, and option 255 is never accepted — it is always written last by the tool itself).

*No corpus evidence — derived from upstream documentation.*
