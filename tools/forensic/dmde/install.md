---
tool: "dmde"
build-system: "scripts"
upstream: "https://dmde.com/download.html"
recipe: "blackarch"
aur-package: "dmde"
source: "https://dmde.com/download.html"
license: "custom:unknown"
blackarch-pkgver: "4.2.2.816"
---

# Installing dmde

> Disk Editor and Data Recovery Software.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dmde) (version `4.2.2.816`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dmde    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://dmde.com/download.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gtk2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dmde]] · [[BlackArch Tools]]

