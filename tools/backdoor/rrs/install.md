---
tool: "rrs"
build-system: "make"
upstream: "http://www.cycom.se/dl/rrs"
recipe: "blackarch"
source: "http://www.cycom.se/dl/rrs"
license: "GPL"
blackarch-pkgver: "1.70"
---

# Installing rrs

> A reverse (connecting) remote shell. Instead of listening for incoming connections it will connect out to a listener (rrs in listen mode). With tty support and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rrs) (version `1.70`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S rrs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.cycom.se/dl/rrs) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make generic
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[rrs]] · [[BlackArch Tools]]

