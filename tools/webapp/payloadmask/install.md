---
tool: "payloadmask"
build-system: "make"
upstream: "https://github.com/CoolerVoid/payloadmask"
recipe: "blackarch"
source: "https://github.com/CoolerVoid/payloadmask"
license: "GPL3"
blackarch-pkgver: "17.58e0525"
---

# Installing payloadmask

> Web Payload list editor to use techniques to try bypass web application firewall.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/payloadmask) (version `17.58e0525`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S payloadmask    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/CoolerVoid/payloadmask) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[payloadmask]] · [[BlackArch Tools]]

