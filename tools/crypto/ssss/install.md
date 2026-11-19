---
tool: "ssss"
ebuild-package: "ssss"
build-system: "make"
upstream: "http://point-at-infinity.org/ssss/"
recipe: "archstrike-pkgbuild"
nix-package: "ssss"
aur-package: "ssss"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ssss"
version: "0.5"
license: "GPL2"
---

# Installing ssss

> Split and Combine Secrets using Shamir's Secret Sharing Scheme

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/ssss) (version `0.5`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S ssss
```

Also on the AUR: `paru -S ssss` (version `0.5.7-1`).

## Nix (works on any distro)

```bash
nix run nixpkgs#ssss    # ad-hoc run, no install
nix profile install nixpkgs#ssss   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://point-at-infinity.org/ssss/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gmp`
  - `xmltoman`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[crypto]] · [[ssss]] · [[ArchStrike Gap Analysis]]
