---
tool: "bss"
build-system: "make"
upstream: "http://www.secuobs.com/news/15022006-bss_0_8.shtml"
recipe: "blackarch"
source: "http://www.secuobs.com/news/15022006-bss_0_8.shtml"
license: "GPL"
blackarch-pkgver: "0.8"
---

# Installing bss

> Bluetooth stack smasher / fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bss) (version `0.8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.secuobs.com/news/15022006-bss_0_8.shtml) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
- ⚠️ Python 2 only deps: python2-pycurl (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bss]] · [[BlackArch Tools]]

