---
name: "maltego-teeth"
categories:
  - recon
version: "1.0+20201218-0parrot2"
homepage: "https://www.maltego.com"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# maltego-teeth

> Offensive Maltego transform pack that drives nmap, sqlmap and friends from the graph.

## What it's for

TEETH is a collection of local Maltego transforms that turn the Maltego (and CaseFile) graph into an attack console: select an entity such as a website or host and run transforms that execute nmap (with NSE script families), sqlmap injection testing, and Metasploit-backed actions locally, returning results as new entities on the graph. It is not a standalone program — it ships the Python transform scripts plus Maltego project files, and everything is driven from the Maltego GUI's transform menus after the pack is imported. It depends on a working Maltego installation and on the tools it wraps (nmap, sqlmap, metasploit-framework) being on PATH. The project dates from the 2013-era Maltego/Kali toolchain and sees no upstream development; expect to fix interpreter paths before the transforms run on a modern Python 3 system. For scripted use, calling nmap or sqlmap directly is simpler — the value here is purely the visual, click-driven workflow inside Maltego.
