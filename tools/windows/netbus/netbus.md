---
name: "netbus"
categories:
  - blackarch-windows
version: "1.6"
homepage: "https://packetstormsecurity.com/files/10320/nb16_p04.zip.html"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# netbus

> NetBus 1.6 — the classic 1998 Windows remote-administration trojan by Carl-Fredrik Neikter, packaged as a legacy sample for study and defense.

## What it's for

NetBus is a 1998 Delphi client–server remote administration tool (marketed by its Swedish author as a prank program; the name translates roughly to "NetPrank") that became one of the most notorious trojans of its era. The server (`PATCH.EXE`, also seen as `SysEdit.exe`) installs itself on the victim — including a registry run key for autostart — and listens as a faceless process on TCP port 12345 (12346 for some auxiliary tasks). The GUI client (`NETBUS.EXE`) connects out and offers keystroke logging and injection, screen captures, file browsing, program launching, CD-tray control, system shutdown and connection tunneling.

The packaged artifact is the NetBus 1.6 patch 4 archive from PacketStorm (`nb16_p04.zip`): the client, the server patch, an RTF manual and a FAQ. The archive's entries are password-protected, so it functions as a distribution sample rather than a ready-to-run toolkit. Today its value is defensive and historical — recognizing the port 12345/12346 signature, building detection for a canonical early RAT, and teaching trojan architecture. The v1.60 client also ran on Unix systems; its client/server protocol is largely textual.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Inventory the sample archive without executing anything

```
unzip -l nb16_p04.zip
```

Outcome: lists the archive's contents — `NETBUS.EXE` (client), `PATCH.EXE` (server installer), `NETBUS.RTF` (manual) and `README.FUQ` — without extracting them; the listing requires no password even though the entries are encrypted, and documents exactly what the sample ships.

### Record the sample for a lab inventory

```
sha256sum nb16_p04.zip
```

Outcome: produces the archive's SHA-256 digest so the sample can be tracked, labelled and compared against reference sets when building a malware-repository or detection-testing corpus for this classic RAT.
