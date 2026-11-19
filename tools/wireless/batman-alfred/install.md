---
tool: "batman-alfred"
build-system: "make"
upstream: "http://www.open-mesh.org/"
recipe: "blackarch"
aur-package: "batman-alfred"
source: "http://www.open-mesh.org/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2026.3"
---

# Installing batman-alfred

> Almighty Lightweight Fact Remote Exchange Daemon.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/batman-alfred) (version `2026.3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S batman-alfred    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.open-mesh.org/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dbus`
  - `gpsd`
  - `libnl`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[batman-alfred]] · [[BlackArch Tools]]

