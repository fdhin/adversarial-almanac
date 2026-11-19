---
name: "ostinato"
categories:
  - blackarch-sniffer
version: "0.9"
homepage: "https://github.com/pstavirs/ostinato/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2020-07-06"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "pstavirs/ostinato"
github-stars: 773
github-pushed: "2024-04-14"
github-archived: true
github-issues: 77
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ostinato

> Cross-platform packet/traffic generator and analyzer with a Qt GUI — "Wireshark in reverse".

## What it's for

Ostinato generates traffic instead of capturing it: you compose packets and streams in a GUI (protocols from ARP/ICMP to TCP/UDP with editable header fields and payloads), set rates, bursts and stream counts, and transmit them on a chosen port to test firewalls, IDS/IPS, QoS or forwarding behavior reproducibly. It ships as two programs: `drone`, the agent that captures and transmits on the traffic-generating host (Linux gives the most accurate rates), and `ostinato`, the Qt GUI client that drives a drone over TCP, default control port 7878. On a single machine the GUI starts its own local drone; distributed setups run drone on the generator host and point the GUI at it. The open-source repository is archived — development moved to ostinato.org — so distros package the final open release (1.2.0, GPLv3); the GUI workflow described here is that release.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Generate traffic on the local machine

Single-host use: the GUI manages its own drone.

```
ostinato
```

Outcome: the Qt GUI opens with a local drone attached; open Port Configuration to pick interfaces, create streams with the protocol wizard, set the transmit rate, then Start Transmission — the configured packets go out the selected port and the port statistics view shows frames and bytes sent.

### Run the agent on a dedicated generator host

Distributed setup: drone on the traffic host, GUI on the workstation.

```
drone -d
```

Outcome: the agent listens on TCP 7878 for GUI control connections (`-d` enables debug logging on stderr); on the workstation run `ostinato -c` (client-only, no local drone) and add the generator's address as a port connection to drive its ports remotely.

### Use a non-default control port

Avoid a clash or host several drones on one machine.

```
drone -p 7890
```

Outcome: the drone's control channel listens on 7890 instead of 7878; point the GUI's port connection at `host:7890`. `drone -v` prints the drone version, revision and libpcap version when you need to confirm what a generator host is running.
