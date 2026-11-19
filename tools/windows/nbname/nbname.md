---
name: "nbname"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://cultdeadcow.com/tools/nbname.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# nbname

> cDc's NetBIOS Name Service tool: passively decodes NBNS traffic on UDP 137 and can attack the protocol with deny, conflict and release abuse.

## What it's for

NBName (Sir Dystic, Cult of the Dead Cow, released at DEF CON 8 in 2000) is a Windows console tool for the NetBIOS Name Service. At its most passive it decodes and displays every NetBIOS name packet it receives on UDP port 137. Its command-line options then manipulate that same protocol offensively: denying name registrations, forcing names into conflict so machines release them, and sweeping networks with adapter-status requests to enumerate name tables. Microsoft's MS00-047 bulletin was issued in direct response to its name-conflict/name-release abuse.

It is far more than a NetBIOS DoS tool: options let it answer queries for chosen names (`/RESPOND` with `/RESPONDIP`), deny everything except a whitelist (`/ALLOW`), redirect through SOCKS 5 proxies, spawn commands when specific services are seen, and export results in lmhosts format. Modern networks rarely expose NBNS beyond the local segment — treat it as a legacy-enumeration and protocol-abuse instrument for lab or period environments.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Passively decode local NBNS traffic

```
NBName
```

Outcome: binds to UDP 137 and prints every NetBIOS name packet it receives — registrations, queries and releases — with the names and service bytes decoded, giving a live view of who is announcing what on the segment.

### Pull the name table from a specific host

```
NBName /ASTAT 198.51.100.10
```

Outcome: sends an adapter status request to the given IP (which does not have to be local) and prints the remote NetBIOS name table — host names, service types and flags — without needing any credentials.

### Sweep a range and log the responders

```
NBName /SWEEP 198.51.100.1 198.51.100.254 /ASOUT names.txt
```

Outcome: sends adapter status requests to every address in the range (paced at 10 packets/second by default, tunable with `/DELAY`), writing each response's contents to `names.txt` — a quick Windows host inventory built purely from NBNS; `/SCAN IPList` does the same from a file of addresses.

### Deny all name registrations (NetBIOS DoS, lab only)

```
NBName /DENY *
```

Outcome: responds negatively to every NetBIOS name registration packet received, so machines on the segment cannot (re)register their names; per the cDc docs, `/QUERY * /CONFLICT /DENY *` together disable an entire local NetBIOS network and prevent machines from rejoining it. Destructive and loud — only for authorized lab networks.
