---
tool: "nzyme"
build-system: "scripts"
upstream: "https://www.nzyme.org/download"
recipe: "blackarch"
source: "https://www.nzyme.org/download"
license: "custom:SSPL"
blackarch-pkgver: "1.2.2"
---

# Installing nzyme

> WiFi defense system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nzyme) (version `1.2.2`, license custom:SSPL).

## Arch / BlackArch

```bash
sudo pacman -S nzyme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.nzyme.org/download) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[nzyme]] · [[BlackArch Tools]]

