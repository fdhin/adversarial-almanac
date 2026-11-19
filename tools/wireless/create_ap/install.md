---
tool: "create_ap"
build-system: "scripts"
upstream: "https://github.com/oblique/create_ap"
recipe: "blackarch"
aur-package: "create_ap"
source: "https://github.com/oblique/create_ap"
license: "BSD"
blackarch-pkgver: "265.462c09f"
---

# Installing create_ap

> A shell script to create a NATed/Bridged Software Access Point.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/create_ap) (version `265.462c09f`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S create_ap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/oblique/create_ap) (the recipe uses a versioned archive).

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
  - `which`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[create_ap]] · [[BlackArch Tools]]

