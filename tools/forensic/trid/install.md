---
tool: "trid"
build-system: "scripts"
upstream: "http://mark0.net/soft-trid-e.html"
recipe: "blackarch"
aur-package: "trid"
source: "http://mark0.net/soft-trid-e.html"
license: "custom:unknown"
blackarch-pkgver: "2.48"
---

# Installing trid

> An utility designed to identify file types from their binary signatures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trid) (version `2.48`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S trid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://mark0.net/soft-trid-e.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `trid-defs`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[trid]] · [[BlackArch Tools]]

