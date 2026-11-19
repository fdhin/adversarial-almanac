---
tool: "stegolego"
build-system: "other"
upstream: "https://github.com/razc411/StegoLeggo"
recipe: "blackarch"
source: "https://github.com/razc411/StegoLeggo"
license: "custom:unknown"
blackarch-pkgver: "8.85354f6"
---

# Installing stegolego

> Simple program for using stegonography to hide data within BMP images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegolego) (version `8.85354f6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S stegolego    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/razc411/StegoLeggo.git
cd StegoLeggo
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc *.c -o stegolego
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[stegolego]] · [[BlackArch Tools]]

