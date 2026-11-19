---
tool: "fatback"
ebuild-package: "fatback"
build-system: "autotools"
upstream: "http://fatback.sourceforge.net/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/fatback"
version: "1.3"
license: "GPL"
---

# Installing fatback

> A *nix tool for recovering files from FAT file systems.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/fatback) (version `1.3`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S fatback
```

The same PKGBUILD is usually on the AUR (`paru -S fatback`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://fatback.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `flex`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
  CPPFLAGS="$CFLAGS -O -Wcpp" ./configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[fatback]] · [[ArchStrike Gap Analysis]]
