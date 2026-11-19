---
name: "sigspotter"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://www.security-projects.com/?SigSpotter"
packaged-by: [blackarch]
blackarch-builddate: "2020-07-23"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sigspotter

> Enumerates Authenticode-signed binaries on a system and summarizes which publishers have signed code on the machine.

## What it's for

SigSpotter is a small Windows utility from the security-projects.com "Security Projects" stable (the same site behind tools such as Unhide and Patriot NG). It searches a hard drive for binaries carrying digital signatures and prints a summarized list of the publishers that signed them — one line per unique signing entity rather than per file, as the tool's page puts it, "which publishers has been signed binaries in your PC". Its niche is audit and triage: establishing which software vendors' code is present on a system, and spotting unusual or unexpected publisher names among binaries that would otherwise blend in. It reads signature metadata only; it does not validate trust chains or modify anything. Distributed both as Python source and as a Py2Exe-built binary.

## Typical use cases

### Inventory the signing publishers present on a drive

```
SigSpotter.exe c:\
```

Outcome: walks the drive, collects the signed binaries and prints `Publishers found:` followed by the unique signing publishers — e.g. `Adobe Systems Incorporated`, `Apple Inc.`, `Microsoft Corporation`, `VMware, Inc.` — a one-screen profile of whose code is installed and a quick way to spot an out-of-place vendor.

*No corpus evidence — derived from upstream documentation.*
