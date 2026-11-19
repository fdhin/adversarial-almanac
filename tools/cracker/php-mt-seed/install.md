---
tool: "php-mt-seed"
build-system: "make"
upstream: "http://www.openwall.com/php_mt_seed/"
recipe: "blackarch"
source: "http://www.openwall.com/php_mt_seed/"
license: "BSD"
blackarch-pkgver: "4.0"
---

# Installing php-mt-seed

> PHP mt_rand() seed cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/php-mt-seed) (version `4.0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S php-mt-seed    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.openwall.com/php_mt_seed/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `php`

## Exact build steps from the recipe

```bash
cd "php_mt_seed-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[php-mt-seed]] · [[BlackArch Tools]]

