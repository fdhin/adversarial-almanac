---
tool: "eindeutig"
build-system: "make"
upstream: "https://www.jonesdykstra.com/"
recipe: "blackarch"
source: "https://www.jonesdykstra.com/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "20050628_1"
provides: ["dbxparse"]
---

# Installing eindeutig

> Examine the contents of Outlook Express DBX email repository files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eindeutig) (version `20050628_1`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S eindeutig    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.jonesdykstra.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "${pkgname}_$pkgver/src"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/dbxparse`

Backlinks: [[forensic]] · [[eindeutig]] · [[BlackArch Tools]]

