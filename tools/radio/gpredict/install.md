---
tool: "gpredict"
build-system: "autotools"
upstream: "http://gpredict.oz9aec.net/"
recipe: "blackarch"
nix-package: "gpredict"
aur-package: "gpredict"
source: "http://gpredict.oz9aec.net/"
license: "GPL"
blackarch-pkgver: "1764.1d80049"
---

# Installing gpredict

> A real-time satellite tracking and orbit prediction application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gpredict) (version `1764.1d80049`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S gpredict    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://gpredict.oz9aec.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `glib2`
  - `goocanvas`
  - `grig`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `hamlib`
  - `intltool`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[radio]] · [[gpredict]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gpredict    # ad-hoc run, no install
nix profile install nixpkgs#gpredict   # or persist it
```
