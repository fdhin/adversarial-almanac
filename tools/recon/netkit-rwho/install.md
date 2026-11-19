---
tool: "netkit-rwho"
build-system: "autotools"
upstream: "http://packages.qa.debian.org/n/netkit-rwho.html"
recipe: "blackarch"
source: "http://packages.qa.debian.org/n/netkit-rwho.html"
license: "BSD"
blackarch-pkgver: "0.17"
---

# Installing netkit-rwho

> Remote who client and server (with Debian patches).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netkit-rwho) (version `0.17`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S netkit-rwho    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packages.qa.debian.org/n/netkit-rwho.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `sh`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  while read patch; do
    patch -Np1 -i "../debian/patches/$patch"
  done < ../debian/patches/series

  ./configure --prefix=/usr --installroot="$pkgdir"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[netkit-rwho]] · [[BlackArch Tools]]

