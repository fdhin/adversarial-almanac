---
name: "ubitack"
categories:
  - blackarch-wireless
version: "0.3"
homepage: "https://code.google.com/archive/p/ubitack/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Seabreg/ubitack"
github-stars: 0
github-pushed: "2015-08-16"
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

# ubitack

> Menu-driven bash toolkit that wires up connection, evil-AP, credential-sniffing and tunnelling tasks for wireless work.

## What it's for

ubitack ("Ubiquitous Attack 'n Connect Tool Kit", v0.3, by toomsec) is
a single interactive bash menu for tasks that otherwise need a dozen
terminals: connecting to access points from reusable per-network config
files, running an evil/rogue access point (hostapd with karma support
plus dnsmasq), capturing credentials on that AP (dsniff, sslstrip,
Wifizoo, Hamster/Ferret), and setting up safe egress in hostile
environments (SSH, OpenVPN and iodine DNS tunnels). It orchestrates
existing tools rather than implementing attacks itself, and it can
install/update the tools it drives. Written for BackTrack 4: expect to
review its hardcoded paths (e.g. `/pentoo/...` locations) and the
configuration block at the top of the script before using it on a
modern distribution. Per-network profiles are `*.wifi` files —
wpa_supplicant `network={}` blocks prefixed with `##KEY=value` lines
for IP, DNS, channel and MAC hints — stored under
`/usr/share/ubitack/conf`. The GitHub repository is a mirror of the
original Google Code project.

## Typical use cases

### Launch the interactive menu

Start a session.

```
sudo ubitack
```

Outcome: the main menu appears — choose the wireless interfaces, then
pick actions such as connecting via a saved `.wifi` profile, bringing
up the evil access point with its DHCP server, starting credential
capture, or raising an SSH/OpenVPN/DNS tunnel to a safe host.

### Keep a per-site connection profile

Store each lab network once and reconnect repeatedly.

```
sudo cp /usr/share/ubitack/conf/sample-wpa.wifi /usr/share/ubitack/conf/example-site.wifi
```

Outcome: a working copy of the annotated sample profile; edit its
`network={ ssid="..." ... }` block and the `##IP=`/`##DNS=`/`##CHANNEL=`
hint lines for your target network — ubitack offers `*.wifi` profiles
when connecting.

*No corpus evidence — derived from upstream sources (ubitack 0.3 tarball: script header and purpose block, conf/ sample profiles; BlackArch PKGBUILD install paths).*
