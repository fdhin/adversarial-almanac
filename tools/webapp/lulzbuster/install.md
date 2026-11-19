---
tool: "lulzbuster"
build-system: "make"
upstream: "http://www.nullsecurity.net/tools/scanner.html"
recipe: "blackarch"
source: "http://www.nullsecurity.net/tools/scanner.html"
license: "custom:unknown"
blackarch-pkgver: "2.0.1"
---

# Installing lulzbuster

> A multithreaded, very fast and smart HTTP(S) directory and file bruteforcer written in C on top of libcurl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lulzbuster) (version `2.0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lulzbuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.nullsecurity.net/tools/scanner.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make clean
  make lulzbuster
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lulzbuster]] · [[BlackArch Tools]]

