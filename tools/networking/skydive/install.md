---
tool: "skydive"
build-system: "scripts"
upstream: "https://github.com/skydive-project/skydive"
recipe: "blackarch"
aur-package: "skydive"
source: "https://github.com/skydive-project/skydive"
license: "Apache"
blackarch-pkgver: "0.28.0"
---

# Installing skydive

> An open source real-time network topology and protocols analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skydive) (version `0.28.0`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S skydive    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skydive-project/skydive) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[skydive]] · [[BlackArch Tools]]

