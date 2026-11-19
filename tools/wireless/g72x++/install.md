---
tool: "g72x++"
build-system: "other"
upstream: "https://www.ps-auxw.de/"
recipe: "blackarch"
source: "https://www.ps-auxw.de/g72x++.tar.bz2"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1"
provides: ["decode-g72x"]
---

# Installing g72x++

> Decoder for the g72x++ codec.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/g72x++) (version `1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S g72x++    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://www.ps-auxw.de/g72x++.tar.bz2
tar xf g72x++.tar.bz2
cd g72x++
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd g72x

  gcc $CFLAGS -o decode-g72x decode.c g*.c bitstream.c
```

## Installed layout (per recipe)

- Commands: `/usr/bin/decode-g72x`

Backlinks: [[wireless]] · [[g72x++]] · [[BlackArch Tools]]

