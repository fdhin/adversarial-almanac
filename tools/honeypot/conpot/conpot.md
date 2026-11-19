---
name: "conpot"
categories:
  - blackarch-honeypot
version: "0.6.0"
homepage: "https://pypi.org/project/Conpot/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL2
meta-checked: "2026-11-09"
github-repo: "mushorg/conpot"
github-stars: 1538
github-pushed: "2026-11-03"
github-archived: false
github-issues: 34
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 101
gh-activity-checked: "2026-11-09"
---

# conpot

> Low-interaction ICS/SCADA honeypot that emulates industrial devices and their protocols to collect intelligence on adversaries targeting industrial control systems.

## What it's for

Conpot (developed under the mushorg organization, in the orbit of The Honeynet Project) is an industrial control system honeypot. Instead of emulating a generic server it serves templates of factory-floor style devices — controllers speaking protocols such as Modbus, SNMP, HTTP and IPMI — so scanners and attackers probing an ICS address space interact with a controllable fake instead of real hardware. Everything a template serves is defined in template files, so the emulated device inventory, ports and data points can be reshaped without writing code, and captured sessions feed analysis of who targets ICS assets and how. It is a collection sensor, not a redirector: it never fronts real PLC traffic.

## Typical use cases

### Serve the default ICS template

Start the honeypot with the bundled default template.

```
conpot --template default
```

Outcome: conpot loads the default template and binds the emulated device ports it defines (for example a Modbus listener and a management HTTP interface); probing connections and the protocol data they exchange are logged per session, ready for analysis or forwarding to a central collector.

*No corpus evidence — derived from upstream documentation.*
