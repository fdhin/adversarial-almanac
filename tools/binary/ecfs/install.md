---
tool: "ecfs"
build-system: "make"
upstream: "https://github.com/elfmaster/ecfs"
recipe: "blackarch"
source: "https://github.com/elfmaster/ecfs"
license: "custom:unknown"
blackarch-pkgver: "305.1758063"
provides: ["ecfs-handler", "ecfs_handler", "et-flip", "et_flip"]
---

# Installing ecfs

> Extended core file snapshot format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ecfs) (version `305.1758063`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ecfs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/elfmaster/ecfs) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `glibc`
  - `libdwarf`

## Exact build steps from the recipe

```bash
cd $pkgname

  make V="prod" B="64"
```

## Installed layout (per recipe)

- Commands: `/usr/bin/ecfs-handler`, `/usr/bin/ecfs_handler`, `/usr/bin/et-flip`, `/usr/bin/et_flip`

Backlinks: [[binary]] · [[ecfs]] · [[BlackArch Tools]]

