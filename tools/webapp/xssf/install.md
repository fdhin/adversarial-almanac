---
tool: "xssf"
ebuild-package: "xssf"
build-system: "scripts"
upstream: "https://code.google.com/p/xssf/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xssf"
version: "3.0"
license: "BSD"
---

# Installing xssf

> A Cross-Site Scripting Framework for metasploit

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xssf) (version `3.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S xssf
```

The same PKGBUILD is usually on the AUR (`paru -S xssf`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/xssf/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[xssf]] · [[ArchStrike Gap Analysis]]
