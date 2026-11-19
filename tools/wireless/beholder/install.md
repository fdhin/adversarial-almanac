---
tool: "beholder"
build-system: "make"
upstream: "http://www.beholderwireless.org/"
recipe: "blackarch"
source: "http://www.beholderwireless.org/"
license: "AMS"
blackarch-pkgver: "0.8.10"
---

# Installing beholder

> A wireless intrusion detection tool that looks for anomalies in a wifi environment.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/beholder) (version `0.8.10`, license AMS).

## Arch / BlackArch

```bash
sudo pacman -S beholder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.beholderwireless.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make sense
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[beholder]] · [[BlackArch Tools]]

