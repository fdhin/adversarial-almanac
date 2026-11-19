---
tool: "batman-adv"
build-system: "make"
upstream: "http://www.open-mesh.net/"
recipe: "blackarch"
aur-package: "batman-adv"
source: "http://www.open-mesh.net/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2026.3"
---

# Installing batman-adv

> Batman kernel module, (included upstream since .38)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/batman-adv) (version `2026.3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S batman-adv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.open-mesh.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnl`
  - `linux-headers`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  export CFLAGS="${CFLAGS} -fplt"
  make KERNELPATH="/usr/lib/modules/$(uname -r)/build"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[batman-adv]] · [[BlackArch Tools]]

