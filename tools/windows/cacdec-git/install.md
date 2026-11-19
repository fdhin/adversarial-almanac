---
tool: "cacdec-git"
ebuild-package: "cacdec-git"
build-system: "cmake"
upstream: "https://github.com/airbus-cert/cacdec"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cacdec-git"
version: "20200309.r5"
license: "unknown"
---

# Installing cacdec-git

> The hidden RDP client recorder

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cacdec-git) (version `20200309.r5`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S cacdec-git
```

The same PKGBUILD is usually on the AUR (`paru -S cacdec-git`).

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/airbus-cert/cacdec) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Python (pip)**: `construct`, `setuptools`
- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[windows]] · [[cacdec-git]] · [[ArchStrike Gap Analysis]]
