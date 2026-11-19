---
tool: "xorstrings"
ebuild-package: "xorstrings"
build-system: "other"
upstream: "http://blog.didierstevens.com/programs/xorstrings/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xorstrings"
version: "0.0.1"
license: "custom"
---

# Installing xorstrings

> A combination of XORSearch tool and the well-known strings command to search for a given string in an XOR, ROL or ROT encoded binary file.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xorstrings) (version `0.0.1`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S xorstrings
```

The same PKGBUILD is usually on the AUR (`paru -S xorstrings`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.didierstevens.com/programs/xorstrings/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "${srcdir}"
  gcc $CFLAGS -o xorstrings XORStrings.c -trigraphs
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[binary]] · [[xorstrings]] · [[ArchStrike Gap Analysis]]
