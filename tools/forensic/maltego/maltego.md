---
name: "maltego"
categories:
  - blackarch-forensic
version: "4.14.0"
homepage: "https://www.maltego.com/downloads/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 1.5
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# maltego

> OSINT and forensics link-analysis GUI by Maltego Technologies (formerly Paterva); the packaged build is Maltego CE.

## What it's for

Maltego is an interactive Java application for open-source intelligence and link analysis: you seed a graph with entities — domains, DNS names, IP addresses, URLs, whois records, persons, companies, social-media accounts — and run "transforms" that query online data sources to expand those entities into relationship graphs. It is a GUI-only workstation tool: the package ships a thin `maltego` shell wrapper that launches the Java client, and there is no scripting surface in this binary — automation happens through the separate transform development kits (e.g. [[canari]]) rather than the launcher. The free Community Edition (CE) requires registering a Maltego account and is capacity-limited relative to the commercial editions. For manual, non-OSINT link analysis of evidence, Paterva's former companion product [[casefile]] filled that role.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the Maltego GUI

The `maltego` command is a launcher; all work happens in the graphical client.

```
maltego
```

Outcome: starts the Java client — splash screen, login prompt for your Maltego/CE account, then the main window with a new empty graph — from which you add entities and run transforms.
