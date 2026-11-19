---
tool: "smplshllctrlr"
build-system: "scripts"
upstream: "https://github.com/z0noxz/smplshllctrlr"
recipe: "blackarch"
source: "https://github.com/z0noxz/smplshllctrlr"
license: "custom:unknown"
blackarch-pkgver: "9.2baf390"
---

# Installing smplshllctrlr

> PHP Command Injection exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smplshllctrlr) (version `9.2baf390`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S smplshllctrlr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/z0noxz/smplshllctrlr) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[smplshllctrlr]] · [[BlackArch Tools]]

