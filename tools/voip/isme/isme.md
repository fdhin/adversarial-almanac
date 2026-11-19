---
name: "isme"
categories:
  - blackarch-voip
version: "0.12"
homepage: "https://packetstormsecurity.com/files/123534/IP-Phone-Scanning-Made-Easy.12.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# isme

> Perl/Tk GUI workbench for scanning and attacking enterprise IP phone deployments from the voice VLAN.

## What it's for

ISMe (IP Phone Scanning Made Easy, v0.12) is a GUI-driven VoIP assessment framework for a tester connected directly to an IP phone VLAN. A single Tk window organises the toolkit into Scanner (generic sweep, SIP scanner, VxWorks active-debug-mode detection), Tools (web bruteforcers for Aastra, Mitel, SNOM and Polycom phones, Cisco SCCP ringer and call-forwarding abuse, extension-mobility abuse, DHCP starvation, DNS resolution, SIP flooding, a PROTOS-based SIP fuzzer and TCP SYN flood) and Exploits (vendor-specific disclosures and bypasses for Aastra, Alcatel, Avaya, Mitel, Polycom, SNOM and others). It has no command-line interface — everything runs from the GUI — and bundles SCCP (Skinny) protocol helpers for the Cisco tools.

## Typical use cases

### Launch the GUI against a phone VLAN

Run it as root on a host attached to the voice VLAN.

```
isme
```

Outcome: opens the Tk application (the wrapper runs `/usr/share/isme/isme.pl`); choose a scanner or tool from the tabs, point it at the target subnet or phone, and results render in the GUI, with scan runs accumulated under its `scan_history` directory.

*No corpus evidence — derived from upstream documentation.*
