---
tool: "emldump"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#emldump"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#emldump"
license: "custom:unknown"
blackarch-pkgver: "0.0.11"
---

# Installing emldump

> Analyze MIME files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/emldump) (version `0.0.11`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S emldump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#emldump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[emldump]] · [[BlackArch Tools]]

