---
tool: "ifuzz"
build-system: "make"
upstream: "http://www.fuzzing.org/"
recipe: "blackarch"
source: "http://www.fuzzing.org/"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing ifuzz

> A binary file fuzzer with several options.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ifuzz) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ifuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.fuzzing.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[ifuzz]] · [[BlackArch Tools]]

