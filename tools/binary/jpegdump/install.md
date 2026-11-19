---
tool: "jpegdump"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#jpegdump"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#jpegdump"
license: "custom:unknown"
blackarch-pkgver: "0.0.7"
---

# Installing jpegdump

> Tool to analyzse JPEG images Reads binary files and parses the JPEG markers inside them.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jpegdump) (version `0.0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S jpegdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#jpegdump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[jpegdump]] · [[BlackArch Tools]]

