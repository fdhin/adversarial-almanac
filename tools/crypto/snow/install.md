---
tool: "snow"
build-system: "make"
upstream: "https://darkside.com.au/snow/index.html"
recipe: "blackarch"
nix-package: "snow"
aur-package: "snow"
source: "https://darkside.com.au/snow/index.html"
license: "Apache-2.0"
blackarch-pkgver: "20130616"
---

# Installing snow

> Steganography program for concealing messages in text files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snow) (version `20130616`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S snow    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://darkside.com.au/snow/index.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[crypto]] · [[snow]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snow    # ad-hoc run, no install
nix profile install nixpkgs#snow   # or persist it
```
