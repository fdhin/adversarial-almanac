---
name: "gsd"
categories:
  - blackarch-windows
version: "1.1"
homepage: "https://vidstromlabs.com/freetools/gsd/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# gsd

> Prints the DACL of any Windows NT service you name on the command line.

## What it's for

GSD (Get Service DACL, Vidstrom Labs, by Alrik Vidstrom) shows the
Discretionary Access Control List of a service — the object-level
permissions on the service itself, not the permissions on its registry
keys. That distinction matters for privilege-escalation review: a weak
service DACL lets a low-privileged user reconfigure or restart a service
that runs as SYSTEM, which is a classic escalation path. The tool takes
the service name as its single parameter (the same name shown in the
Services control-panel applet) and requires the calling account to be a
member of the local Administrators group. It is a small, flagless console
utility: run it with no arguments and it prints its usage instructions.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Read a service's DACL

Check who can reconfigure a given service.

```
GSD.exe Spooler
```

Outcome: prints the DACL of the named service — the trustees and their
access rights on the service object — revealing whether non-admin
accounts hold modify, start/stop or change-config rights worth abusing.

### Capture the DACL to a file

Record the permissions for review or comparison.

```
GSD.exe Spooler > spooler-dacl.txt
```

Outcome: writes the same DACL listing to `spooler-dacl.txt` instead of
the console, so permissions can be attached to findings or diffed after
remediation.
