---
tool: "gpshell"
ebuild-package: "gpshell"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/globalplatform/"
recipe: "archstrike-pkgbuild"
aur-package: "gpshell"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/gpshell"
version: "1.4.4"
license: "GPL3"
---

# Installing gpshell

> GlobalPlatform command shell

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/gpshell) (version `1.4.4`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S gpshell
```

Also on the AUR: `paru -S gpshell` (version `2.4.0-1`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/globalplatform/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `globalplatform`

## Exact build steps from the recipe

```bash
cd $srcdir/$pkgname-$pkgver
  ./configure --prefix=/usr --sysconfdir=/etc
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[hardware]] · [[gpshell]] · [[ArchStrike Gap Analysis]]
