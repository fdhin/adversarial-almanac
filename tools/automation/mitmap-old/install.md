---
tool: "mitmap-old"
build-system: "scripts"
upstream: "http://www.darkoperator.com/tools-and-scripts/"
recipe: "blackarch"
source: "http://www.darkoperator.com/tools-and-scripts/"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing mitmap-old

> Shell Script for launching a Fake AP with karma functionality and launches ettercap for packet capture and traffic manipulation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitmap-old) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S mitmap-old    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.darkoperator.com/tools-and-scripts/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `bash`
  - `dhcp`
  - `ettercap`
  - `iptables`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[mitmap-old]] · [[BlackArch Tools]]

