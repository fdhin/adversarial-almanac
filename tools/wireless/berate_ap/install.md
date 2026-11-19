---
tool: "berate_ap"
build-system: "scripts"
upstream: "https://github.com/sensepost/berate_ap"
recipe: "blackarch"
source: "https://github.com/sensepost/berate_ap"
license: "BSD-2-Clause"
blackarch-pkgver: "v0.4.6.r100.ged4ba9b"
---

# Installing berate_ap

> Script for orchestrating mana rogue WiFi Access Points.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/berate_ap) (version `v0.4.6.r100.ged4ba9b`, license BSD-2-Clause).

## Arch / BlackArch

```bash
sudo pacman -S berate_ap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sensepost/berate_ap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `dnsmasq`
  - `hostapd`
  - `iproute2`
  - `iptables`
  - `iw`
  - `procps-ng`
  - `util-linux`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[berate_ap]] · [[BlackArch Tools]]

