---
name: "spade"
categories:
  - blackarch-windows
version: "114"
homepage: "http://www.samspade.org/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2016-12-31"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# spade

> Sam Spade for Windows — the classic all-in-one Internet investigation toolkit for tracing spam and network abuse.

## What it's for

Sam Spade for Windows v1.14 (Steve Atkins, samspade.org) is a general-purpose Internet utility package "with some extra features to help in tracing the source of spam and other forms of Internet harassment" — the product's own description and the reason it became the standard abuse-desk desktop of the late 1990s and 2000s. It bundles the investigator's everyday tools behind one GUI: whois with correct registrar routing for domains and IP blocks, DNS queries including zone transfers, traceroute, ping, TCP connects, SMTP checks including relay testing and address verification, and a basic HTTP fetch view. Everything can be chained interactively — a lookup's output feeds the next query with a right-click. Today the same functions live in `whois`, `dig` and web APIs, but the tool remains a compact, offline-capable reference for manual network investigation, and its name still shows up whenever spam tracing history is discussed. GUI application, no command-line interface; the original site survives only in archives, and the redistributed `spade114.exe` binary is the reference copy.

*No corpus evidence — derived from upstream documentation.*
