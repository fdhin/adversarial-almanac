---
name: "ipv6toolkit"
categories:
  - blackarch-scanner
version: "861.9ee80b6"
homepage: "http://www.si6networks.com/tools/ipv6toolkit/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "fgont/ipv6toolkit"
github-stars: 278
github-pushed: "2024-12-02"
github-archived: false
github-issues: 12
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipv6toolkit

> Fernando Gont's SI6 Networks IPv6 Toolkit: the reference C suite for IPv6 security assessment — `scan6` host discovery (including pattern-based remote scans), `addr6` address analysis, and a dozen per-protocol tools (`ra6`, `na6`, `icmp6`, `frag6`, `tcp6`, ...).

*No corpus evidence — derived from upstream documentation (README.TXT and the manuals/ man pages of fgont/ipv6toolkit, v2.2; examples below are the manual's own EXAMPLES with placeholders substituted).*

## What it's for

The SI6 Networks IPv6 Toolkit is the standard toolbox for the parts of IPv6 that nmap-era habits don't cover. Its insight is that a /64 cannot be brute-forced, so `scan6` discovers hosts by the patterns real addresses follow: local-link scans via ICMPv6, and remote scans keyed on IEEE-OUI vendor slugs, IPv4-embedded addresses, low-byte addresses of virtual machines, and known-IID/known-prefix wordlists — a registry of scanning strategies formalized in RFC 7707. Alongside discovery sit analysis (`addr6`), reconnaissance of the SLAAC/ND attack surface (`na6`, `rs6`, `rd6`), and tools for assessing specific IPv6/extension-header behavior (`frag6`, `icmp6`, `jumbo6`, `mldq6`, `path6`, `flow6`, `blackhole6`, `tcp6`, `udp6`, `ni6`, `script6`) — several of which send intentionally nonconformant traffic for compliance testing. `make` builds C sources against libpcap. These are powerful assessment tools: run them only on networks you are authorized to test, since several generate exactly the packets DoS attacks are made of.

## Typical use cases

### Discover neighbors on the local link

```
scan6 -i eth0 -L -e -v
```

Outcome: Scans the local network (`-L`) on interface eth0 with verbose output, printing the unique global-scope addresses (`-e`) of responding neighbors — the IPv6 equivalent of an ARP sweep, and typically the fastest full host census you can get.

### Remote-scan a /64 for virtual-machine addresses

```
scan6 -d 2001:db8::/64 --tgt-virtual-machines all
```

Outcome: Instead of probing 2^64 addresses, generates the candidate IIDs that VMs (VMware, VirtualBox, Hyper-V and peers) characteristically use and probes only those — remote hosts that answer identify themselves as likely virtual machines. Combine with `--ipv4-embedded ipv4-32 --ipv4-host 198.51.100.0/24` when targets embed their IPv4 address in the IPv6 one.

### Remote-scan keyed on a hardware vendor

```
scan6 -d 2001:db8::/64 --tgt-vendor 'Dell Inc' -p tcp
```

Outcome: Derives candidate addresses from the IEEE OUI blocks registered to the named vendor (their EUI-64/slac addresses embed the MAC) and probes with TCP (`-p tcp`) instead of ICMP — hosts answering are Dell hardware at address patterns no brute force would have found.

### Analyze and summarize address sets

```
addr6 -a 2001:db8::1
cat addrs.txt | addr6 -i -q -s
```

Outcome: The first command dissects a single address (scope, type, IID pattern such as low-byte/isatap/teredo-embedded); the second reads a list on stdin, deduplicates it (`-q`) and prints statistics (`-s`) about the address types observed — the quantitative check for whether a target's addresses follow exploitable patterns.
