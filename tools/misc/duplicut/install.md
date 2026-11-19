---
tool: "duplicut"
build-system: "make"
upstream: "https://github.com/nil0x42/duplicut"
recipe: "blackarch"
aur-package: "duplicut"
source: "https://github.com/nil0x42/duplicut"
license: "GPL3"
blackarch-pkgver: "2.2"
---

# Installing duplicut

> Remove duplicates from massive wordlist, without sorting it (for dictionnary-based password cracking).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/duplicut) (version `2.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S duplicut    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/nil0x42/duplicut) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `make`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[duplicut]] · [[BlackArch Tools]]

