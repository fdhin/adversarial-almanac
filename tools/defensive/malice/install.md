---
tool: "malice"
build-system: "scripts"
upstream: "https://github.com/maliceio/malice"
recipe: "blackarch"
source: "https://github.com/maliceio/malice"
license: "Apache"
blackarch-pkgver: "0.3.28"
---

# Installing malice

> VirusTotal Wanna Be - Now with 100% more Hipster.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malice) (version `0.3.28`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S malice    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/maliceio/malice) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[malice]] · [[BlackArch Tools]]

