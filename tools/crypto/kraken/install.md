---
tool: "kraken"
build-system: "other"
upstream: "http://opensource.srlabs.de/projects/a51-decrypt"
recipe: "blackarch"
source: "http://opensource.srlabs.de/projects/a51-decrypt"
license: "GPL3"
blackarch-pkgver: "32.368a837"
---

# Installing kraken

> A project to encrypt A5/1 GSM signaling using a Time/Memory Tradeoff Attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kraken) (version `32.368a837`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S kraken    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://opensource.srlabs.de/projects/a51-decrypt) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname/Kraken"

  ./build.sh
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[kraken]] · [[BlackArch Tools]]

