---
tool: "ghostpop3"
ebuild-package: "ghostpop3"
build-system: "autotools"
upstream: "http://kernel.embedromix.ro/us/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ghostpop3"
version: "0.1.0"
license: "GPL"
---

# Installing ghostpop3

> A program that simulates an 'always no messages' POP3 server

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ghostpop3) (version `0.1.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S ghostpop3
```

The same PKGBUILD is usually on the AUR (`paru -S ghostpop3`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://kernel.embedromix.ro/us/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "${pkgname}-${pkgver}"
    ./configure --prefix=/usr
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[honeypot]] · [[ghostpop3]] · [[ArchStrike Gap Analysis]]
