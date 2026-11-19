---
name: "fakenetbios"
categories:
  - blackarch-spoof
version: "7.b83701e"
homepage: "https://github.com/mubix/FakeNetBIOS"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "mubix/FakeNetBIOS"
github-stars: 44
github-pushed: "2012-12-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fakenetbios

> Patrick Chambet's FakeNetBIOS (2004, mirrored on GitHub by mubix): two daemons that make one machine impersonate a whole fleet of Windows hosts on a LAN — answering NetBIOS name lookups and broadcasting browse announcements.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Faking Windows presence at the NetBIOS layer. `FakeNetbiosNS` listens on UDP 137 and answers NetBIOS name-service requests exactly like a real Windows box, so `nbtstat -A`, `ping -a` and friends see the host you invented; `FakeNetbiosNS` can also poison with Release packets. `FakeNetbiosDGM` emits periodic datagram announcements on UDP 138 that populate Network Neighborhood / the Computer Browser service — one box can announce itself as hundreds of workstations, a domain master, or the file server, which is where the man-in-the-middle and honeypot uses come from ("NetBIOS as an ARP-like protocol", in the author's words). Both tools run standalone or as honeyd responders (`-H`), and both build on Linux and Windows. On BlackArch the binaries install as `fakenetbiosns` and `fakenetbiosdgm`. Era caveat: written against Windows XP-era browsing; modern Windows networks have largely retired NBT-NS browsing, though name-service spoofing still catches misconfigured environments.

## Typical use cases

### Impersonate a Windows host on the network

One sentence of intent: make lookups resolve a chosen machine name to your position on the LAN.

```
sudo fakenetbiosns -s 198.51.100.10 -d 198.51.100.255 -D CORP -N WORKSTN01 -v
```

Outcome: answers NetBIOS name requests for the `WORKSTN01*` names in workgroup `CORP` as if sent by `198.51.100.10`; `-v` prints each exchange. On Windows the host's own NBT must be disabled on that adapter to free UDP 137.

### Populate the network browser with fake machines

One sentence of intent: announce a fleet of workstations from a single box.

```
sudo fakenetbiosdgm -s 198.51.100.10 -d 198.51.100.255 -D CORP -N WORKSTN -n 100 -t 1000 -v
```

Outcome: broadcasts browser announcements every second (`-t` milliseconds) creating 100 hosts named `WORKSTN1`…`WORKSTN100` in `CORP`; they appear in the Network window within minutes. `-a` selects the announcement type (1 host, 2 domain/workgroup, 3 local master) and `-c` sets each host's description string.

### Run as a honeyd responder

One sentence of intent: scriptable simulation inside a honeypot instead of raw packets.

```
fakenetbiosdgm -d 198.51.100.255 -D CORP -N WORKSTN -H
```

Outcome: `-H` switches to honeyd mode (paired with `-u` on the DGM side to skip raw IP), letting a honeyd framework own the packet crafting while the tool supplies the NetBIOS protocol behavior — the intended deployment for large-scale deception, since thousands of announced hosts cost one process rather than one raw-socket storm.
