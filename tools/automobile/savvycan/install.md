---
tool: "savvycan"
build-system: "make"
upstream: "https://github.com/collin80/SavvyCAN"
recipe: "blackarch"
nix-package: "savvycan"
aur-package: "savvycan"
source: "https://github.com/collin80/SavvyCAN"
license: "MIT"
blackarch-pkgver: "continuous.QT6.r0.ge56f5a3"
provides: ["help"]
---

# Installing savvycan

> QT-based CAN bus analysis tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/savvycan) (version `continuous.QT6.r0.ge56f5a3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S savvycan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/collin80/SavvyCAN) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `qt5-base`
  - `qt5-declarative`
  - `qt5-serialbus`
  - `qt5-tools`

## Exact build steps from the recipe

```bash
cd $_pkgname

  sed -i -e '/.*isEmpty(PREFIX)/,+3d' SavvyCAN.pro
  # https://github.com/collin80/SavvyCAN/issues/853
  sed -i -e 's#share/icons#share#g' SavvyCAN.pro
  qmake-qt5 PREFIX=/usr \
    QMAKE_CFLAGS="${CFLAGS}" \
    QMAKE_CXXFLAGS="${CXXFLAGS}" \
    QMAKE_LFLAGS="${LDFLAGS}"
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/help`

Backlinks: [[automobile]] · [[savvycan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#savvycan    # ad-hoc run, no install
nix profile install nixpkgs#savvycan   # or persist it
```
