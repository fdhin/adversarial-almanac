---
tool: "hex2bin"
build-system: "make"
upstream: "http://hex2bin.sourceforge.net/"
recipe: "blackarch"
aur-package: "hex2bin"
source: "http://hex2bin.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "2.5"
provides: ["mot2bin"]
---

# Installing hex2bin

> Converts Motorola and Intel hex files to binary.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hex2bin) (version `2.5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hex2bin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://hex2bin.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "Hex2bin-$pkgver"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/mot2bin`
- Data: `/usr/share/man`

Backlinks: [[binary]] · [[hex2bin]] · [[BlackArch Tools]]

