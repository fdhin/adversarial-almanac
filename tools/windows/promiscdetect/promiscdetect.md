---
name: "promiscdetect"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://vidstromlabs.com/freetools/promiscdetect"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# promiscdetect

> Local check whether a Windows network adapter is running in promiscuous mode — the tell-tale of a sniffer on the box.

## What it's for

PromiscDetect (by Arne Vidström, vidstromlabs.com / ntsecurity.nu) checks the local network adapters of a Windows machine for promiscuous mode, which usually means a sniffer — a packet capture tool, or an attacker's traffic-intercepting implant — is running on the computer. It was the first tool that could perform this check on Windows. The check is strictly local: it inspects the machine's own adapter state, so run it on the host being audited, not remotely. It prints the packet filters active per adapter; an adapter in normal mode shows the Directed, Multicast and Broadcast filters, and a promiscuous adapter is flagged as such. Interpret results asymmetrically: a positive report almost certainly means promiscuous mode, but a negative one is not proof, since a sophisticated attacker can tamper with what the tool sees.

## Typical use cases

### Audit a host for running sniffers

Run the check from a Command Prompt on the machine under audit.

```
promiscdetect.exe
```

Outcome: prints one block per network adapter showing its filter state; an adapter reported in promiscuous mode indicates capture software is listening on it. Append `> adapters.txt` to keep the audit output as a file.

*No corpus evidence — derived from upstream documentation.*
