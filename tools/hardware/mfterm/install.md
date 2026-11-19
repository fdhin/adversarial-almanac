---
tool: "mfterm"
ebuild-package: "mfterm"
build-system: "autotools"
upstream: "https://github.com/4ZM/mfterm"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mfterm"
version: "1.0.7"
license: "GPL"
---

# Installing mfterm

> Terminal for working with Mifare Classic 1-4k tags

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mfterm) (version `1.0.7`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S mfterm
```

The same PKGBUILD is usually on the AUR (`paru -S mfterm`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/4ZM/mfterm) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `automake`
- **System libraries** (Arch name — translate for your distro):
  - `libnfc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `readline`

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver
  ./autogen.sh
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[hardware]] · [[mfterm]] · [[ArchStrike Gap Analysis]]
