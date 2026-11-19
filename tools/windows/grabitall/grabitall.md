---
name: "grabitall"
categories:
  - blackarch-windows
version: "1.1"
homepage: "http://ntsecurity.nu/toolbox/grabitall/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# grabitall

> ARP-spoofing traffic redirector for Windows 2000/XP/2003 that reroutes traffic through the attacker's machine.

## What it's for

GrabItAll (Arne Vidstrom, 2002) performs traffic redirection by sending
spoofed ARP replies: it can redirect traffic addressed to one other
computer to the attacking machine, or make traffic between two other
computers flow through the attacker, where it can be inspected. For the
two-host mode it can also enable or disable IP routing on the local
machine. It is a legacy, driver-based tool: the shipped EthDrv protocol
driver (EthDrv.inf/EthDrv.sys) must be installed on the network connection
first, and it targets Windows 2000/XP/2003 era systems. As a built-in
tell, every run sends a UDP datagram to port 7777 containing an
identification string, which makes active use detectable on the segment.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Launch the redirector and pick an attack mode

Start GrabItAll interactively on the attacker host.

```
grabitall.exe
```

Outcome: prints a numbered menu of attack modes — (1) redirect traffic
between two other computers through this computer, (2) redirect traffic
meant for another computer to this computer, (3) enable routing on this
computer, (4) disable routing — and prompts for the mode and target
addresses; selecting a mode starts sending the spoofed ARP replies
("Performing redirection.").

### Detect GrabItAll activity on the wire

Watch for the tool's identification beacon.

```
tcpdump -i eth0 udp port 7777
```

Outcome: any GrabItAll instance on the segment shows up as a UDP datagram
to port 7777 carrying its identification string — a cheap detector for
this specific tool during lab exercises or incident triage.
