---
name: "dnsa"
gentoo-category: "net-analyzer"
version: "0.5-r1"
homepage: "http://packetfactory.openwall.net/projects/dnsa/index.html"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dnsa

> Packet-crafting DNS auditing tool: DNS ID spoofing, transaction-ID sniffing and cache-poisoning attacks.

## What it's for

dnsa (DNS Auditor, by Pierre Betouin; part of the packetfactory tool set)
is a swiss-army knife for attacking DNS that builds and injects raw packets
itself via libnet/libpcap — it is not a resolver-side script. It offers
three modes: answering observed DNS queries with forged replies (ID
spoofing), sniffing a host's DNS transaction IDs to feed prediction, and
injecting additional-record cache poisoning attempts. On switched networks
you need ARP spoofing first so the tool sees the victim's queries. The
upstream site is long dead; the tool is historical and needs root plus a
libnet 1.0 toolchain to build.

## Typical use cases

### Forge DNS answers for a victim's queries

Spoof the resolved IP for a hostname.

```
dnsa -1 -D www.example.com -S 198.51.100.10 -s 198.51.100.20 -i eth0
```

Outcome: listens on `eth0` and, whenever 198.51.100.20 asks about
`www.example.com` (`-D`; omit it to answer every sniffed query), sends a
forged reply claiming the answer is 198.51.100.10 (`-S`); the victim's
resolver cache then holds the attacker-chosen address.

### Sniff a resolver's DNS transaction IDs

Harvest ID state for prediction.

```
dnsa -2 -s 198.51.100.20 -w ids.txt
```

Outcome: passively records the DNS transaction IDs used by the querying
host 198.51.100.20 into `ids.txt` (`-w`); the sequence is raw material for
predicting future IDs (the mode is marked beta/unfinished upstream).

### Attempt cache poisoning via additional records

Inject a forged record alongside a legitimate-looking answer.

```
dnsa -3 -D attacker.example.com -S 198.51.100.5 -s 198.51.100.20 -a www.example.com -b 198.51.100.10 -i eth0
```

Outcome: sends the victim resolver (198.51.100.20) a crafted response for
`attacker.example.com` whose additional record maps `www.example.com`
(`-a`) to 198.51.100.10 (`-b`); a vulnerable resolver that trusts the
additional section caches the poisoned entry.

*No corpus evidence — derived from upstream source (dnsa 0.5 tarball:
usage.c and docs/EXAMPLES, archived packetfactory release).*
