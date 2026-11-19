---
tool: "twofi"
build-system: "scripts"
upstream: "http://www.digininja.org/projects/twofi.php"
recipe: "blackarch"
source: "http://www.digininja.org/projects/twofi.php"
license: "CCPL"
blackarch-pkgver: "2.0"
kali-package: "twofi"
kali-version: "2.0-0kali2"
---

# Installing twofi

> Twitter Words of Interest.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/twofi) (version `2.0`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S twofi    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install twofi    # Kali package version 2.0-0kali2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.digininja.org/projects/twofi.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`
  - `ruby-pkg-config`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[twofi]] · [[BlackArch Tools]]

