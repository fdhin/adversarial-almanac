---
name: "thc-ipv6"
categories:
  - information-gathering
version: "3.8-4"
homepage: "https://github.com/vanhauser-thc/thc-ipv6"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-02-07"
build-system: make
meta-checked: "2026-11-09"
github-repo: "vanhauser-thc/thc-ipv6"
github-stars: 1205
github-pushed: "2026-04-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# thc-ipv6

> The Hacker's Choice IPv6 attack toolkit — a suite of ~50 focused tools for MITM, rogue-router, enumeration and DoS on local IPv6 segments.

## What it's for

THC-IPv6 by vanhauser-thc (vh@thc.org) is the reference toolkit for attacking the IPv6 protocols that run alongside IPv4 on virtually every modern LAN: NDP (IPv6's ARP equivalent), router advertisements, DHCPv6, MLD and ICMPv6. Because most networks trust these protocols unauthenticated, the suite's members can insert an attacker as man-in-the-middle (parasite6, redir6), impersonate infrastructure (fake_router6, fake_dhcps6), enumerate hosts invisible to IPv4 scanning (alive6, dnsdict6), and deny service to new devices (dos-new-ip6). The tools are Linux-only, work on Ethernet, are intentionally noisy (detectable by IDS, by design), and are meant for authorized security assessment of your own segments. Each binary prints its full syntax when run without options. Kali packages the members with an `atk6-` name prefix (e.g. atk6-alive6, atk6-parasite6); the examples below use the unprefixed names shipped by Arch. A typical workflow is discovery first (alive6, dnsdict6, passive_discovery6), then MITM (parasite6 or fake_router6 + fake_dhcps6), then interception. All member syntax below is verified against the packaged source's usage output.

## Typical use cases

### alive6 — discover live IPv6 hosts on the local link

Find every system answering on the segment before choosing targets.

```
alive6 eth0
```

Outcome: probes the local network and prints every IPv6 address that responds — the host inventory for everything that follows (add -d to also reverse-DNS resolve live addresses, -i/-o to read targets from or write results to a file).

### dnsdict6 — brute-force DNS for AAAA names

Enumerate a domain's IPv6 (and IPv4) records by dictionary.

```
dnsdict6 -d4 -m example.com
```

Outcome: runs a threaded dictionary attack against example.com's DNS using the medium built-in wordlist (-s small through -x huge, or pass your own file), printing every name that resolves, with -d4 including A records.

### parasite6 — NDP man-in-the-middle

Insert yourself between IPv6 hosts and their neighbors.

```
parasite6 -l eth0
```

Outcome: answers IPv6 neighbor solicitations on eth0, claiming the attacker's MAC for targets' addresses (the IPv6 equivalent of ARP spoofing) and placing the attacker inline for traffic between local hosts; -l keeps it running in a loop.

### fake_router6 — announce a rogue IPv6 router

Make hosts adopt the attacker as their default router.

```
fake_router6 eth0 2001:db8::/64
```

Outcome: sends high-priority router advertisements for 2001:db8::/64 on eth0, so IPv6-enabled clients install the attacker as their default gateway and start handing it their traffic; optional trailing arguments set the announced DNS server, router link-local address, MTU and MAC.

### fake_dhcps6 — run a rogue DHCPv6 server

Hand out addresses and, crucially, a DNS server of your choosing.

```
fake_dhcps6 eth0 2001:db8:1::/64 2001:db8:1::53
```

Outcome: answers DHCPv6 requests on eth0 with addresses from the given prefix and the attacker-controlled DNS server 2001:db8:1::53 — clients that prefer DHCPv6 over RA now resolve names through you, enabling full traffic interception.

### fake_mld6 — spoof multicast listener membership

Join or leave multicast groups on behalf of others.

```
fake_mld6 eth0 query
```

Outcome: in query mode, asks the network which hosts listen to which multicast addresses and prints the reports; in add/delete mode it advertises or removes membership of a chosen multicast group for any victim, manipulating multicast flows.

### fake_advertise6 — claim an IPv6 address you do not own

Poison the neighbor cache with a forged advertisement.

```
fake_advertise6 eth0 2001:db8::10
```

Outcome: sends neighbor advertisements asserting that 2001:db8::10 lives at the attacker's MAC address, redirecting traffic addressed to that host to the attacker's machine.

### redir6 — ICMPv6 redirect man-in-the-middle

Re-route a victim's traffic through the attacker with a spoofed redirect.

```
redir6 eth0 2001:db8::100 2001:db8::1 2001:db8::1 2001:db8::254
```

Outcome: sends a crafted ICMPv6 redirect to victim 2001:db8::100 telling it to reach target 2001:db8::1 via the attacker (new-router 2001:db8::254), intelligently pivoting that flow through the attacker's machine; without a valid new-router MAC this degrades into a DoS.

### toobig6 — implant a small MTU on a target

Force a victim to shrink its packets.

```
toobig6 eth0 2001:db8::100 2001:db8::1 1280
```

Outcome: sends spoofed packet-too-big messages to the target claiming an MTU of 1280 for the path, shrinking (and fragmenting) the traffic the victim sends toward the chosen existing address — a lever for path-MTU attacks and interception.

### detect-new-ip6 — watch for new IPv6 devices

Trigger on every host that joins the segment.

```
detect-new-ip6 eth0 /usr/local/bin/handle-new-host
```

Outcome: passively detects duplicate-address-detection traffic and prints each new IPv6 address as it joins; when a script is given, it is executed as `handle-new-host <new-ip> <interface>` for every arrival, chaining automated scanning onto new devices.

### dos-new-ip6 — deny service to new IPv6 devices

Break address configuration for arriving hosts.

```
dos-new-ip6 eth0
```

Outcome: listens for new IPv6 addresses attempting to join and immediately spoofs the neighbor-solicitation replies so each victim believes its chosen address already collides, preventing it from configuring that address (IPv6 DAD DoS).

*No corpus evidence — derived from upstream documentation and member usage() output from the packaged source.*
