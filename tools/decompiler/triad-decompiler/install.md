---
tool: "triad-decompiler"
ebuild-package: "triad-decompiler"
build-system: "make"
upstream: "https://github.com/electrojustin/triad-decompiler"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/triad-decompiler"
version: "0.4.3"
license: "custom"
---

# Installing triad-decompiler

> Capstone based x86 decompiler for ELF binaries

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/triad-decompiler) (version `0.4.3`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S triad-decompiler
```

The same PKGBUILD is usually on the AUR (`paru -S triad-decompiler`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/electrojustin/triad-decompiler) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make triad
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[decompiler]] · [[triad-decompiler]] · [[ArchStrike Gap Analysis]]
