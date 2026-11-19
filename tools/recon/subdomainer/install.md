---
tool: "subdomainer"
build-system: "scripts"
upstream: "http://www.edge-security.com/subdomainer.php"
recipe: "blackarch"
source: "http://www.edge-security.com/subdomainer.php"
license: "GPL2"
blackarch-pkgver: "1.2"
---

# Installing subdomainer

> A tool designed for obtaining subdomain names from public sources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subdomainer) (version `1.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S subdomainer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.edge-security.com/subdomainer.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[subdomainer]] · [[BlackArch Tools]]

