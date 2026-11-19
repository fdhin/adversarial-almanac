---
name: "etherchange"
categories:
  - blackarch-windows
version: "1.1"
homepage: "https://vidstromlabs.com/freetools/etherchange/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# etherchange

> Interactive console tool that changes the Ethernet (MAC) address of Windows network adapters.

## What it's for

EtherChange, by Alrik Vidstrom of Vidstrom Labs, changes the Ethernet hardware address of the network adapters in a Windows machine — useful when testing DHCP reservations, 802.1X/NAC bindings, or any policy that keys on the adapter's MAC. It is a small interactive console program: you launch it from an administrator Command Prompt and it walks you through picking an adapter and entering the new address; it takes no command-line options. The account must be in the local Administrators group, and double-clicking the executable from Explorer is pointless — the console window closes immediately, so it must be run from a Command Prompt. The change is a software-level override of the adapter's reported address, not a permanent rewrite of the card's firmware.

## Typical use cases

### Change a network adapter's MAC address

Launch the tool from an elevated Command Prompt and follow its interactive prompts.

```
etherchange.exe
```

Outcome: a console menu walks you through selecting a network adapter and entering the new Ethernet address, printing the on-screen instructions needed to complete and apply the change.

*No corpus evidence — derived from upstream documentation.*
