---
tool: "blackhash"
build-system: "make"
upstream: "http://16s.us/blackhash/"
recipe: "blackarch"
source: "http://16s.us/blackhash/"
license: "GPL"
blackarch-pkgver: "0.2"
---

# Installing blackhash

> Creates a filter from system hashes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blackhash) (version `0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S blackhash    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://16s.us/blackhash/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `crypto++`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[blackhash]] · [[BlackArch Tools]]

