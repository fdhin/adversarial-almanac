---
tool: "jsql-injection"
build-system: "scripts"
upstream: "https://github.com/ron190/jsql-injection"
recipe: "blackarch"
source: "https://github.com/ron190/jsql-injection"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.115"
kali-package: "jsql-injection"
kali-version: "0.115-0kali1"
---

# Installing jsql-injection

> A Java application for automatic SQL database injection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jsql-injection) (version `0.115`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S jsql-injection    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install jsql-injection    # Kali package version 0.115-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ron190/jsql-injection) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jsql-injection]] · [[BlackArch Tools]]

