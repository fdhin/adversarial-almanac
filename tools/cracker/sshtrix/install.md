---
tool: "sshtrix"
build-system: "make"
upstream: "http://nullsecurity.net/tools/cracker.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/cracker.html"
license: "custom"
blackarch-pkgver: "0.0.3"
---

# Installing sshtrix

> A very fast multithreaded SSH login cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sshtrix) (version `0.0.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S sshtrix    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/cracker.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libssh`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make sshtrix
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[sshtrix]] · [[BlackArch Tools]]

