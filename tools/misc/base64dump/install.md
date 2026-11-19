---
tool: "base64dump"
build-system: "scripts"
upstream: "https://blog.didierstevens.com/my-software/#base64dump"
recipe: "blackarch"
source: "https://blog.didierstevens.com/my-software/#base64dump"
license: "custom:unknown"
blackarch-pkgver: "0.0.14"
---

# Installing base64dump

> Extract and decode base64 strings from files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/base64dump) (version `0.0.14`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S base64dump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blog.didierstevens.com/my-software/#base64dump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[base64dump]] · [[BlackArch Tools]]

