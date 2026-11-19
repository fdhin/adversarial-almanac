---
name: "sub7"
categories:
  - blackarch-windows
version: "2.2"
homepage: "https://dl.packetstormsecurity.net/trojans/Subseven.2.2.zip"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "EgeBalci/sub7"
github-stars: 20
github-pushed: "2023-10-03"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sub7

> Sub7 2.2 — mobman's 2001-era Delphi remote-access trojan, packaged as a legacy artifact for study and detection.

## What it's for

Sub7 (written in Delphi by "mobman", final major versions around 2001) was one of the defining Windows trojans of the late 1990s and early 2000s, alongside NetBus and Back Orifice. The distributed archive is the complete kit: the GUI client `sub7.exe` (skinnable), the `server.exe` implant, the `sin.exe` loader, `EditServer.exe` for configuring the server (port, password, notification), support libraries (`ICQMAPI.dll`, `PACKET32.DLL`, `ZPACKET.VXD`), a plugin system (`s7passwords.dll`, `s7sniffer.dll`, `s7scanner.dll`, `s7keys.dll`, `s7takeover.dll`, `icqpwsteal.dll`, `matrix.dll` and more), CGI notification scripts for ICQ/webpage-based owner notification, and command/menu configuration files (`commands.cfg`, `menu.cfg`, `pages.cfg`). That architecture — a configurable server, a plugin DLL ecosystem and out-of-band notification — is the direct ancestor of modern RAT design, which is the tool's main value today: detection engineering, malware-history reference and teaching. Note that the archive's `readme.txt` is password-protected in the distribution zip; treat the whole package as a malware sample, not as runnable software.

## Typical use cases

### Inventory the kit without executing anything

```
unzip -l Subseven.2.2.zip
```

Outcome: lists every archive member — client, server, loader, EditServer, the plugin DLLs, configs and skins — without extracting or running anything, documenting exactly what the sample ships (the listing itself needs no password even though extracting some entries does).

### Fingerprint the sample for a detection corpus

```
sha256sum Subseven.2.2.zip
```

Outcome: prints the archive's SHA-256 digest so the sample can be tracked, labelled and matched against reference sets when building a malware repository or testing detection coverage for this classic RAT family.

*No corpus evidence — derived from upstream documentation.*
