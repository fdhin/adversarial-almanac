---
name: "inzider"
categories:
  - blackarch-windows
version: "1.2"
homepage: "https://vidstromlabs.com/freetools/inzider/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# inzider

> Lists which Windows processes listen on which ports, including IPv6, svchost service names, and port-open times.

## What it's for

Inzider (Vidstrom Labs) maps listening ports to the processes that opened them — it was the first tool that could do this on Windows, back in the 1990s. The current version runs on Windows 10, shows IPv6 bindings, resolves the services hosted inside svchost.exe, and reports the date and time each port was opened — useful for spotting rogue or backdoor listeners and telling service ports from unauthorized ones. It is a console tool without flags: it must be run from a Command Prompt (double-clicking flashes a window and exits) and simply prints its table. It reports the mapping only; it does not inspect or kill processes.

## Typical use cases

### List processes and their listening ports

```
inzider.exe
```

Outcome: prints a table of listening ports with the owning process, address family (IPv4 and IPv6), any svchost.exe service information, and when each port was opened. An address of `0.0.0.0` means the process bound the port on all interfaces. Run from an elevated Command Prompt for the most information.

### Save the port map to a file

```
inzider.exe > ports.txt
```

Outcome: writes the same table to ports.txt for later review or diffing against a later run.

*No corpus evidence — derived from upstream documentation.*
