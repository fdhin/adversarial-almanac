---
tool: "cisco7crack"
build-system: "other"
upstream: "https://github.com/madrisan/cisco7crack"
recipe: "blackarch"
aur-package: "cisco7crack"
source: "https://github.com/madrisan/cisco7crack"
license: "GPL2"
blackarch-pkgver: "2.f1c21dd"
---

# Installing cisco7crack

> Crypt and decrypt the cisco enable 7 passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco7crack) (version `2.f1c21dd`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cisco7crack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/madrisan/cisco7crack) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cisco7crack]] · [[BlackArch Tools]]

