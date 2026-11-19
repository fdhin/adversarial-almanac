---
tool: "thcsmartbrute"
ebuild-package: "thcsmartbrute"
build-system: "autotools"
upstream: "https://github.com/vanhauser-thc/THC-Archive/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/thcsmartbrute"
version: "1.0"
license: "GPL3"
---

# Installing thcsmartbrute

> A tool that finds undocumented and secret commands implemented in a smartcard.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/thcsmartbrute) (version `1.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S thcsmartbrute
```

The same PKGBUILD is usually on the AUR (`paru -S thcsmartbrute`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/vanhauser-thc/THC-Archive/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `pcsclite`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[thcsmartbrute]] · [[ArchStrike Gap Analysis]]
