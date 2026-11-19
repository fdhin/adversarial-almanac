---
tool: "oscanner"
build-system: "scripts"
upstream: "http://www.cqure.net/wp/oscanner/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/oscanner/"
license: "GPL"
blackarch-pkgver: "1.0.6"
provides: ["reportviewer"]
kali-package: "oscanner"
kali-version: "1.0.6-1kali4"
---

# Installing oscanner

> An Oracle assessment framework developed in Java.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/oscanner) (version `1.0.6`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S oscanner    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install oscanner    # Kali package version 1.0.6-1kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/oscanner/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- Commands: `/usr/bin/reportviewer`

Backlinks: [[fuzzer]] · [[oscanner]] · [[BlackArch Tools]]

