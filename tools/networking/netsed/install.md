---
tool: "netsed"
build-system: "make"
upstream: "http://silicone.homelinux.org/projects/netsed/"
recipe: "blackarch"
aur-package: "netsed"
source: "http://silicone.homelinux.org/projects/netsed/"
license: "GPL2"
blackarch-pkgver: "1.3"
---

# Installing netsed

> Small and handful utility design to alter the contents of packets forwarded thru network in real time.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netsed) (version `1.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S netsed    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://silicone.homelinux.org/projects/netsed/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[netsed]] · [[BlackArch Tools]]

