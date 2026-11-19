---
tool: "netkit-rusers"
build-system: "autotools"
upstream: "https://packages.debian.org/source/sid/net/netkit-rusers"
recipe: "blackarch"
source: "https://packages.debian.org/source/sid/net/netkit-rusers"
license: "BSD"
blackarch-pkgver: "0.17"
---

# Installing netkit-rusers

> Logged in users; Displays who is logged in to machines on local network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netkit-rusers) (version `0.17`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S netkit-rusers    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://packages.debian.org/source/sid/net/netkit-rusers) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libtirpc-compat`
  - `rpcsvc-proto`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --installroot="$pkgdir"

  make LDFLAGS=-ltirpc
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[netkit-rusers]] · [[BlackArch Tools]]

