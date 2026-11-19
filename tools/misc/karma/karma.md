---
name: "karma"
gentoo-category: "net-misc"
version: "20060124"
homepage: "http://www.theta44.org/karma"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, nixpkgs]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# karma

> 2006 wireless client attack toolkit whose rogue access point answers every probed SSID.

## What it's for

KARMA ("KARMA Attacks Radioed Machines Automatically", Dino A. Dai Zovi and Shane Macaulay) is a research proof-of-concept for attacking the way 802.11 clients select networks. A passive sniffer watches Probe Request frames to learn each client's preferred/trusted networks; a rogue access point built from patches to the Linux MADWifi driver and hostapd then responds to probes and association requests for any SSID, so nearby clients that auto-join known names attach to the attacker instead. Once a client is associated, higher-level fake services can capture credentials or exercise client-side vulnerabilities. The framework was last updated in 2006, depends on the long-obsolete MADWifi driver generation, and ships without exploits ("bring your own exploits"); its concept lives on in Karmetasploit, Jasager, the WiFi Pineapple and similar rogue-AP tooling, while modern clients no longer auto-join probed networks. It is of historical and teaching interest rather than a practical tool today, and no documented command-line workflow is verifiable from the upstream — hence this note describes the tool without examples.

*No corpus evidence — derived from upstream documentation.*
