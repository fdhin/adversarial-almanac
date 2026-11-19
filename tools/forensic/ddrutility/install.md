---
tool: "ddrutility"
ebuild-package: "ddrutility"
build-system: "make"
upstream: "http://sourceforge.net/projects/ddrutility/"
recipe: "archstrike-pkgbuild"
nix-package: "ddrutility"
aur-package: "ddrutility"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ddrutility"
version: "2.8"
license: "GPL3"
---

# Installing ddrutility

> Set of utilities for use with GNU ddrescue to aid with data recovery

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ddrutility) (version `2.8`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S ddrutility
```

Also on the AUR: `paru -S ddrutility` (version `2.8-2`).

## Nix (works on any distro)

```bash
nix run nixpkgs#ddrutility    # ad-hoc run, no install
nix profile install nixpkgs#ddrutility   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/ddrutility/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `sleuthkit`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}" || exit
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[ddrutility]] · [[ArchStrike Gap Analysis]]
