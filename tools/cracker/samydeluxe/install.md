---
tool: "samydeluxe"
build-system: "scripts"
upstream: "http://github.com/jensp/samydeluxe"
recipe: "blackarch"
source: "http://github.com/jensp/samydeluxe"
license: "unknown"
blackarch-pkgver: "2.2ed1bac"
---

# Installing samydeluxe

> Automatic samdump creation script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/samydeluxe) (version `2.2ed1bac`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S samydeluxe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://github.com/jensp/samydeluxe) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `samdump2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[samydeluxe]] · [[BlackArch Tools]]

