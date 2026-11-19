---
tool: "densityscout"
build-system: "scripts"
upstream: "https://www.cert.at/en/downloads/software/software-densityscout"
recipe: "blackarch"
source: "https://www.cert.at/en/downloads/software/software-densityscout"
license: "ISC"
blackarch-pkgver: "45"
---

# Installing densityscout

> Calculates density for files of any file-system-path to finally output an accordingly descending ordered list.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/densityscout) (version `45`, license ISC).

## Arch / BlackArch

```bash
sudo pacman -S densityscout    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.cert.at/en/downloads/software/software-densityscout) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[densityscout]] · [[BlackArch Tools]]

