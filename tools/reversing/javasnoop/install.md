---
tool: "javasnoop"
build-system: "scripts"
upstream: "https://code.google.com/p/javasnoop/"
recipe: "blackarch"
source: "https://code.google.com/p/javasnoop/"
license: "GPL-3.0-only"
blackarch-pkgver: "1.1"
kali-package: "javasnoop"
kali-version: "1.1-rc2-1kali4"
---

# Installing javasnoop

> A tool that lets you intercept methods, alter data and otherwise hack Java applications running on your computer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/javasnoop) (version `1.1`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S javasnoop    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install javasnoop    # Kali package version 1.1-rc2-1kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/javasnoop/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[javasnoop]] · [[BlackArch Tools]]

