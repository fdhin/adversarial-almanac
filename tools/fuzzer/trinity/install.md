---
tool: "trinity"
build-system: "autotools"
upstream: "http://codemonkey.org.uk/projects/trinity/"
recipe: "blackarch"
nix-package: "trinity"
source: "http://codemonkey.org.uk/projects/trinity/"
license: "GPL"
blackarch-pkgver: "16494.ef2118f8c"
---

# Installing trinity

> A Linux System call fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trinity) (version `16494.ef2118f8c`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S trinity    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://codemonkey.org.uk/projects/trinity/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[trinity]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#trinity    # ad-hoc run, no install
nix profile install nixpkgs#trinity   # or persist it
```
