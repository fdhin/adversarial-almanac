---
tool: "kwprocessor"
ebuild-package: "kwprocessor"
build-system: "make"
upstream: "https://github.com/hashcat/kwprocessor"
recipe: "archstrike-pkgbuild"
aur-package: "kwprocessor"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/kwprocessor"
version: "1.00"
license: "MIT"
---

# Installing kwprocessor

> Advanced keyboard-walk generator with configureable basechars, keymap and routes

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/kwprocessor) (version `1.00`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S kwprocessor
```

Also on the AUR: `paru -S kwprocessor` (version `1.00-1`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/hashcat/kwprocessor) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver/
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/kwp`

Backlinks: [[cracker]] · [[kwprocessor]] · [[ArchStrike Gap Analysis]]
