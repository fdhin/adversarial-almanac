---
tool: "netcon"
build-system: "scripts"
upstream: "https://github.com/BlackArch/netcon"
recipe: "blackarch"
source: "https://github.com/BlackArch/netcon"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing netcon

> A network connection establishment and management script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netcon) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S netcon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/netcon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[netcon]] · [[BlackArch Tools]]

