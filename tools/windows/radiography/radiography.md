---
name: "radiography"
categories:
  - blackarch-windows
version: "2"
homepage: "http://www.security-projects.com/?RadioGraPhy"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: windows
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# radiography

> Windows incident-response grabber that sweeps a live system for persistence, accounts, network state and hidden processes in one pass.

## What it's for

RadioGraPhy is a free (GPL) Windows forensic tool from Security Projects that grabs as much information as possible from a running Windows system: startup-related registry keys, Internet Explorer settings, system accounts and their properties, startup files, services, the hosts file, scheduled tasks, loaded drivers, NetBIOS shares, hidden processes (via its companion WinUnhide) and network state such as open connections and listening ports. Two features set it apart from plain inventory scripts: whenever it identifies a process — running, configured in startup locations, or in Task Scheduler — it checks the binary's hash against Team Cymru's Malware Hash Registry to flag known threats, and it can dump a copy of the event log plus the process binaries themselves for later review. It ships with both a CLI version and a graphical frontend, but there is no documented scripting surface, so it is run directly on the host being audited and its findings read as a report.

*No corpus evidence — derived from upstream documentation. Description-only: no verifiable CLI invocation — upstream documents a GUI plus an unspecified CLI build.*
