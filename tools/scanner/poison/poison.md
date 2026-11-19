---
name: "poison"
categories:
  - blackarch-scanner
version: "1.5.41"
homepage: "http://nologin.org/main.pl?action=codeList&"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# poison

> A fast, asynchronous SYN and UDP port scanner from the nologin.org toolset.

## What it's for

Poison is a small network port scanner distributed as a source tarball from the nologin.org code site (download host `warlord.nologin.org`, whose scanner tools are authored by "warlord"). It performs fast, asynchronous TCP SYN and UDP scanning and matches responses against a shipped fingerprint database (`fps.txt`, installed to `/etc/poison/fps.txt`) to identify services; the packaged binary is `/usr/bin/poison`. The author's site is no longer reachable, the tarball is not captured by public web archives, and no faithful mirror is verifiable — so no reliable CLI documentation survives. This note is deliberately description-only: no usage examples are given because no verifiable usage reference (README, man page or retrievable source) exists. The GitHub repository previously recorded in the vault frontmatter is an unrelated Elixir JSON library that happens to share the name.
