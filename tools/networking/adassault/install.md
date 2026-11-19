---
tool: "adassault"
build-system: "scripts"
upstream: "https://noraj.github.io/ADAssault/"
recipe: "blackarch"
source: "https://noraj.github.io/ADAssault/"
license: "MIT"
blackarch-pkgver: "0.0.4.r29.g7a85b8e"
provides: ["ada"]
---

# Installing adassault

> An Active Directory environments pentest tool complementary to existing ones like NetExec.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adassault) (version `0.0.4.r29.g7a85b8e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S adassault    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://noraj.github.io/ADAssault/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-bundler`
  - `ruby-erb`

## Installed layout (per recipe)

- Commands: `/usr/bin/ada`

Backlinks: [[networking]] · [[adassault]] · [[BlackArch Tools]]

