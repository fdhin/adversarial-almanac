---
tool: "pngcheck"
build-system: "make"
upstream: "http://www.libpng.org/pub/png/apps/pngcheck.html"
recipe: "blackarch"
nix-package: "pngcheck"
aur-package: "pngcheck"
source: "http://www.libpng.org/pub/png/apps/pngcheck.html"
license: "GPL"
blackarch-pkgver: "3.0.3"
provides: ["png-fix-IDAT-windowsize", "pngsplit"]
---

# Installing pngcheck

> Verifies the integrity of PNG, JNG and MNG files by checking the CRCs and decompressing the image data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pngcheck) (version `3.0.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S pngcheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.libpng.org/pub/png/apps/pngcheck.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpng`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/png-fix-IDAT-windowsize`, `/usr/bin/pngsplit`
- Data: `/usr/share/man`

Backlinks: [[stego]] · [[pngcheck]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pngcheck    # ad-hoc run, no install
nix profile install nixpkgs#pngcheck   # or persist it
```
