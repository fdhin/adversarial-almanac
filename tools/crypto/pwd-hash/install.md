---
tool: "pwd-hash"
build-system: "make"
upstream: "http://vladz.devzero.fr/pwd-hash.php"
recipe: "blackarch"
source: "http://vladz.devzero.fr/pwd-hash.php"
license: "GPL3"
blackarch-pkgver: "2.0"
---

# Installing pwd-hash

> A password hashing tool that use the crypt function to generate the hash of a string given on standard input.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwd-hash) (version `2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pwd-hash    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://vladz.devzero.fr/pwd-hash.php) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[pwd-hash]] · [[BlackArch Tools]]

