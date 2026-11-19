---
name: "sdn-toolkit"
categories:
  - blackarch-networking
version: "1.21"
homepage: "http://downloads.sourceforge.net/project/sdn-toolkit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Seabreg/sdn-toolkit"
github-stars: 0
github-pushed: "2019-09-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sdn-toolkit

> Discovery, enumeration and attack utilities for SDN controllers and OpenFlow networks.

## What it's for

SDN Toolkit (Gregory Pickett, Hellfire Security) is a set of Python command-line tools for testing software-defined networks: scanning for SDN controllers and fingerprinting them through their REST APIs, enumerating OpenFlow switches, dumping and injecting flow rules, flooding controller flow tables, and impersonating a switch. It speaks both to the northbound REST APIs of common controllers (Floodlight, OpenDaylight, ONOS, Brocade, HP VAN, OpenContrail and Cisco APIC, catalogued in its config.ini) and to the OpenFlow wire protocol itself (default TCP 6633). Use it to audit the exposure and hardening of SDN infrastructure you are authorized to test — the flow-injection and flooding tools are directly disruptive.

## Typical use cases

### Scan for and fingerprint an SDN controller

Determine whether a host runs an SDN controller and which product it is.

```
of-scan 198.51.100.10
```

Outcome: probes the known controller REST APIs on the target (port with `-p`, credentials with `-u`/`-pw`, both defaulting to admin) and reports the detected controller type and version along with what the API exposes; add `-a` to stop after detection.

### Enumerate OpenFlow-capable devices

Check which addresses on a range actually speak OpenFlow.

```
of-enum targets.txt
```

Outcome: for each address listed one-per-line in targets.txt, attempts an OpenFlow exchange on TCP 6633 (`-p` to change) and reports the devices that respond.

### Dump the installed flow rules

Read the current flow table through the controller.

```
of-map 198.51.100.10 -p 8080 -u admin -pw admin
```

Outcome: authenticates to the controller's REST API and prints the flow rules currently programmed across the fabric — the configuration baseline before any rule changes.

### Inject a flow rule

Add attacker-chosen rules through the same API admins use.

```
of-access 198.51.100.10 -u admin -pw admin
```

Outcome: writes OpenFlow flow rules (e.g. allow or drop selected traffic) via the controller API, demonstrating what default credentials or an exposed API let an attacker change.

### Flood a controller's flow table

Test resilience against table-exhaustion denial of service.

```
of-flood 198.51.100.10
```

Outcome: bombards the controller on TCP 6633 with flow requests until tables fill or the controller degrades; the sibling test `of-switch 198.51.100.10` instead impersonates an OpenFlow switch to see what the controller hands a rogue device.

*No corpus evidence — derived from upstream documentation.*
