---
tool: "luksipc"
build-system: "make"
upstream: "http://www.johannes-bauer.com/linux/luksipc"
recipe: "blackarch"
source: "http://www.johannes-bauer.com/linux/luksipc"
license: "GPL3"
blackarch-pkgver: "0.01"
---

# Installing luksipc

> A tool to convert unencrypted block devices to encrypted LUKS devices in-place.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/luksipc) (version `0.01`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S luksipc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.johannes-bauer.com/linux/luksipc) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[luksipc]] · [[BlackArch Tools]]

