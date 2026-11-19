---
tool: "ms-sys"
build-system: "make"
upstream: "http://ms-sys.sourceforge.net/"
recipe: "blackarch"
nix-package: "ms-sys"
aur-package: "ms-sys"
source: "http://ms-sys.sourceforge.net/"
license: "GPL2"
blackarch-pkgver: "2.8.0"
---

# Installing ms-sys

> A tool to write Win9x- master boot records (mbr) under linux - RTM!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ms-sys) (version `2.8.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ms-sys    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://ms-sys.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make PREFIX="/usr"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[ms-sys]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ms-sys    # ad-hoc run, no install
nix profile install nixpkgs#ms-sys   # or persist it
```
