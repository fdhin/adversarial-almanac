---
tool: "tomb"
ebuild-package: "tomb"
build-system: "make"
upstream: "http://www.dyne.org/software/${pkgname}"
recipe: "archstrike-pkgbuild"
nix-package: "tomb"
aur-package: "tomb"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/tomb"
version: "2.9"
license: "GPL3"
---

# Installing tomb

> A system for easy encryption and backup of personal files

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/tomb) (version `2.9`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S tomb
```

Also on the AUR: `paru -S tomb` (version `2.13-2`).

## Nix (works on any distro)

```bash
nix run nixpkgs#tomb    # ad-hoc run, no install
nix profile install nixpkgs#tomb   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.dyne.org/software/${pkgname}) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `cryptsetup`
  - `gnupg`
  - `gtk2`
  - `libnotify`
  - `pinentry`
  - `sudo`
  - `zsh`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${_pkgname}-${pkgver}/extras/gtk-tray"
  make

  cd ../kdf-keys
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[crypto]] · [[tomb]] · [[ArchStrike Gap Analysis]]
