---
name: "ipscan"
categories:
  - blackarch-scanner
version: "3.9.3"
homepage: "https://github.com/angryip/ipscan"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-11-13"
build-system: scripts
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "angryip/ipscan"
github-stars: 5189
github-pushed: "2026-11-02"
github-archived: false
github-issues: 192
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 190
gh-activity-checked: "2026-11-09"
---

# ipscan

> Angry IP Scanner — the long-running cross-platform GUI IP address and port scanner: point it at a range, get a live table of hosts with ping, hostname, ports and other per-address details.

*No corpus evidence — derived from upstream documentation (angryip/ipscan README and official site; a GUI application, so the use case is its launch-and-sweep workflow).*

## What it's for

Angry IP Scanner (angryip.org) is the friendly face of network discovery: a Java/SWT desktop application (Java 21+ for current releases) that scans an address range with multithreaded ping (ICMP, TCP or UDP variants) and optional port probes, then presents every address in a spreadsheet-like table populated by "fetchers" — packets sent, hostname, TTL, latency, filtered port counts, comment/Web detection fields. Found hosts can be exported to CSV, TXT or XML for documentation. It is a GUI program without a scripting/headless mode, so it fits interactive surveys of a lab or LAN segment rather than pipelines — for scripted sweeps use nmap or masscan. The `ipscan` launcher installed by the distro packages starts the GUI; range and port settings live inside the application window.

## Typical use cases

### Launch the GUI and sweep a subnet

```
ipscan
```

Outcome: Opens the Angry IP Scanner window; enter a range (e.g. `198.51.100.1-254`), pick ports in Preferences, press Start, and the live table fills with one row per address showing alive/dead status, hostname, latency and open ports; export the results to CSV via the menu for a documented inventory.
