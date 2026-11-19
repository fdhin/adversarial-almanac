---
tool: "udp-hunter"
build-system: "scripts"
upstream: "https://github.com/NotSoSecure/udp-hunter"
recipe: "blackarch"
source: "https://github.com/NotSoSecure/udp-hunter"
license: "Apache"
blackarch-pkgver: "4.b95cce5"
---

# Installing udp-hunter

> Network assessment tool for various UDP Services covering both IPv4 and IPv6 protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udp-hunter) (version `4.b95cce5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S udp-hunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NotSoSecure/udp-hunter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `ifaddr`, `netaddr`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[udp-hunter]] · [[BlackArch Tools]]

