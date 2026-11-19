---
tool: "facebrok"
build-system: "scripts"
upstream: "https://github.com/PowerScript/facebrok"
recipe: "blackarch"
source: "https://github.com/PowerScript/facebrok"
license: "custom:unknown"
blackarch-pkgver: "33.0f6fe8d"
---

# Installing facebrok

> Social Engineering Tool Oriented to facebook.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/facebrok) (version `33.0f6fe8d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S facebrok    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/PowerScript/facebrok) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mariadb`
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[facebrok]] · [[BlackArch Tools]]

