---
tool: "dhcpig"
build-system: "scripts"
upstream: "https://github.com/kamorin/DHCPig"
recipe: "blackarch"
nix-package: "dhcpig"
source: "https://github.com/kamorin/DHCPig"
license: "GPL-2.0-or-later"
blackarch-pkgver: "v2.7.3.r13.gc519fab"
kali-package: "dhcpig"
kali-version: "1.6-3"
---

# Installing dhcpig

> Enhanced DHCPv4 and DHCPv6 exhaustion and fuzzing script written in python using scapy network library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dhcpig) (version `v2.7.3.r13.gc519fab`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S dhcpig    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dhcpig    # Kali package version 1.6-3
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/kamorin/DHCPig.git
cd DHCPig
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[dhcpig]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dhcpig    # ad-hoc run, no install
nix profile install nixpkgs#dhcpig   # or persist it
```
