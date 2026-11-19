---
tool: "ratproxy"
build-system: "make"
upstream: "http://code.google.com/p/ratproxy/"
recipe: "blackarch"
source: "http://code.google.com/p/ratproxy/"
license: "APACHE"
blackarch-pkgver: "1.58"
---

# Installing ratproxy

> A passive web application security assessment tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ratproxy) (version `1.58`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S ratproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://code.google.com/p/ratproxy/) (the recipe uses a versioned archive).

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

Backlinks: [[fuzzer]] · [[ratproxy]] · [[BlackArch Tools]]

