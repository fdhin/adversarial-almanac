---
tool: "ext4magic"
build-system: "autotools"
upstream: "https://sourceforge.net/projects/ext4magic/"
recipe: "blackarch"
nix-package: "ext4magic"
source: "https://sourceforge.net/projects/ext4magic/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.3.2"
kali-package: "ext4magic"
kali-version: "0.3.2-15"
---

# Installing ext4magic

> File carver used when recovering from disasters or in digital forensics activities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ext4magic) (version `0.3.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ext4magic    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ext4magic    # Kali package version 0.3.2-15
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/ext4magic/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bzip2`
  - `e2fsprogs`
  - `file`
  - `util-linux`

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[ext4magic]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ext4magic    # ad-hoc run, no install
nix profile install nixpkgs#ext4magic   # or persist it
```
