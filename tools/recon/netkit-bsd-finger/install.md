---
tool: "netkit-bsd-finger"
build-system: "autotools"
upstream: "http://ftp.linux.org.uk/pub/linux/Networking/netkit"
recipe: "blackarch"
aur-package: "netkit-bsd-finger"
source: "http://ftp.linux.org.uk/pub/linux/Networking/netkit"
license: "BSD"
blackarch-pkgver: "0.17"
---

# Installing netkit-bsd-finger

> BSD-finger ported to Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netkit-bsd-finger) (version `0.17`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S netkit-bsd-finger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://ftp.linux.org.uk/pub/linux/Networking/netkit) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "bsd-finger-$pkgver"

   ./configure --prefix=/usr

   make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[netkit-bsd-finger]] · [[BlackArch Tools]]

