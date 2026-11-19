---
tool: "timegen"
build-system: "make"
upstream: "https://bastianborn.de/radio-clock-hack/"
recipe: "blackarch"
source: "https://bastianborn.de/radio-clock-hack/"
license: "custom:unknown"
blackarch-pkgver: "0.4"
---

# Installing timegen

> This program generates a *.wav file to "send" an own time signal to DCF77 compatible devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/timegen) (version `0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S timegen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://bastianborn.de/radio-clock-hack/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[timegen]] · [[BlackArch Tools]]

