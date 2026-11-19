---
tool: "dhcpf"
build-system: "other"
upstream: "https://github.com/elceef/dhcpf"
recipe: "blackarch"
source: "https://github.com/elceef/dhcpf"
license: "custom:unknown"
blackarch-pkgver: "3.a770b20"
provides: ["dhcpf"]
---

# Installing dhcpf

> Passive DHCP fingerprinting implementation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dhcpf) (version `3.a770b20`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dhcpf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/elceef/dhcpf) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./Build.sh
```

## Installed layout (per recipe)

- Commands: `/usr/bin/dhcpf`

Backlinks: [[fingerprint]] · [[dhcpf]] · [[BlackArch Tools]]

