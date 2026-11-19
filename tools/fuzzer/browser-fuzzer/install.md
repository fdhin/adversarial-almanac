---
tool: "browser-fuzzer"
build-system: "make"
upstream: "http://www.krakowlabs.com/dev.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/fuzzer/bf3.tar.gz"
license: "GPL3"
blackarch-pkgver: "3"
provides: ["bf3"]
---

# Installing browser-fuzzer

> Browser Fuzzer 3

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/browser-fuzzer) (version `3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S browser-fuzzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO https://dl.packetstormsecurity.net/fuzzer/bf3.tar.gz
tar xf bf3.tar.gz
cd bf3
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "bf$pkgver"

  make clean
  make CC="gcc $CFLAGS"
```

## Installed layout (per recipe)

- Commands: `/usr/bin/bf3`

Backlinks: [[fuzzer]] · [[browser-fuzzer]] · [[BlackArch Tools]]

