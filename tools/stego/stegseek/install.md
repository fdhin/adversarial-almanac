---
tool: "stegseek"
build-system: "cmake"
upstream: "https://github.com/RickdeJager/stegseek"
recipe: "blackarch"
nix-package: "stegseek"
aur-package: "stegseek"
source: "https://github.com/RickdeJager/stegseek"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.6.r10.g5896978"
---

# Installing stegseek

> Lightning fast steghide cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegseek) (version `v0.6.r10.g5896978`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S stegseek    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/RickdeJager/stegseek) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libjpeg-turbo`
  - `libmcrypt`
  - `mhash`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake -B build -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release..
  make -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[stegseek]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#stegseek    # ad-hoc run, no install
nix profile install nixpkgs#stegseek   # or persist it
```
