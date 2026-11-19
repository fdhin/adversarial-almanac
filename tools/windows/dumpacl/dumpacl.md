---
name: "dumpacl"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://www.systemtools.com/cgi-bin/download.pl?DumpAcl"
packaged-by: [blackarch]
blackarch-builddate: "2019-12-24"
build-system: windows
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dumpacl

> SomarSoft's classic Windows NT utility that reports permissions, ownership and audit settings.

## What it's for

DumpAcl is the 1990s SomarSoft utility (later SystemTools) that dumps NT filesystem and registry ACLs, ownership and audit settings as formatted reports — the era's standard instrument for permission reviews of Windows NT and 2000 systems. SomarSoft folded it into their commercial line and renamed it DumpSec: the packaged download URL serves a DumpSec download page today, and SystemTools' Hyena carries the same reporting forward. The packaged artifact is only the original Windows setup program (`SystemTools.EXE`, a GUI installer — not the tool's binary), and no verifiable command-line interface for the original DumpAcl survives in current or archived upstream documentation, so no runnable examples are given. For practical ACL enumeration on modern Windows use icacls or accesschk, or SystemTools' successor tools.
