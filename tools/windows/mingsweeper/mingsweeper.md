---
name: "mingsweeper"
categories:
  - blackarch-windows
version: "1.00"
homepage: "http://www.hoobie.net/mingsweeper/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mingsweeper

> Windows GUI network reconnaissance tool from the author of Brutus: high-speed host discovery and identification across large address spaces.

## What it's for

MingSweeper (1.00 alpha 5, build 130, distributed from hoobie.net by the author of the Brutus web cracker) is a network reconnaissance tool designed for large address space, high-speed node discovery and identification. In one GUI it combines ICMP ping sweeps, reverse DNS sweeps, TCP port scans (full connect, SYN, NULL, FIN, XMAS and ACK filter scans), UDP port scans, operating system identification and application identification.

OS fingerprinting takes an NMAP-style TCP/IP stack fingerprint plus ICMP-based tests (TTL measurement and four ICMP query types under varying flags, following Ofir Arkin's ICMP research); application identification works by banner grabbing. Results land in filterable/searchable views and can be saved and reloaded, with flexible target range specification.

It is a Win32 GUI application with no command-line interface — Windows 2000/XP are required for the raw-socket features (SYN scans and OS identification), NT 4.0 only supports connect-style scanning. It is unmaintained 2001-era software; reach for nmap/masscan instead unless you specifically need a period Windows-native GUI. Description-only: no scriptable surface exists.

*No corpus evidence — derived from upstream documentation.*
