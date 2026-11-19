---
name: "browselist"
categories:
  - blackarch-windows
version: "1.4"
homepage: "https://vidstromlabs.com/browselist/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# browselist

> Arne Vidstrom's Windows console tool that dumps the NetBIOS browse list — every computer name a Windows network advertises, with the role each machine plays.

## What it's for

BrowseList (a Vidstrom Labs free tool) retrieves the browse list maintained by the Windows Computer Browser services: the computer names visible in the workgroup/domain and the roles they hold — PDC, BDC, domain master browser, master/backup/potential browser, stand-alone server, workstation — along with each machine's system comments. Run without parameters it asks your own system for the list; give it an IP address and it asks that host instead, making it a one-command census of a Windows segment from a single vantage point. It is a small standalone console executable (BlackArch ships it under /usr/share/windows/browselist/browselist.exe, runnable under Wine) speaking classic NetBIOS browsing — most useful against legacy Windows networks and for documenting what an old segment still advertises. It only reads the browse list; it does not enumerate shares or authenticate (see NetView from the same author for share enumeration).

## Typical use cases

### Dump the local browse list

```
browselist.exe
```

Outcome: prints every computer name the local system's browser sees, each with its roles (PDC, BDC, server, workstation, browser roles) and its system comment — an instant inventory of the Windows machines on the local segment.

### Enumerate through a remote host

```
browselist.exe 198.51.100.10 > browse.txt
```

Outcome: asks the host at 198.51.100.10 for its browse list instead of the local one and writes it to browse.txt — one query from one machine collects the names and roles visible from that host's vantage point.

*No corpus evidence — derived from upstream documentation.*
