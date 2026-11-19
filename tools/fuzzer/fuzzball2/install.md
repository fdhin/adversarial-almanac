---
tool: "fuzzball2"
build-system: "make"
upstream: "http://nologin.org/"
recipe: "blackarch"
source: "http://nologin.org/"
license: "custom"
blackarch-pkgver: "0.7"
---

# Installing fuzzball2

> A fuzzer for TCP and IP protocol options. It sends a bunch of more or less bogus packets to the target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuzzball2) (version `0.7`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S fuzzball2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://nologin.org/) (the recipe uses a versioned archive).

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

Backlinks: [[fuzzer]] · [[fuzzball2]] · [[BlackArch Tools]]

