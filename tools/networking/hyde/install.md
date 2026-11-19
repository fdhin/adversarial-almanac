---
tool: "hyde"
build-system: "make"
upstream: "https://github.com/CoolerVoid/Hyde"
recipe: "blackarch"
source: "https://github.com/CoolerVoid/Hyde"
license: "custom:unknown"
blackarch-pkgver: "16.0931514"
---

# Installing hyde

> Just another tool in C to do DDoS (with spoofing).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hyde) (version `16.0931514`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hyde    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/CoolerVoid/Hyde) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[hyde]] · [[BlackArch Tools]]

