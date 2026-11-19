---
tool: "vpnpivot"
build-system: "autotools"
upstream: "https://github.com/0x36/VPNPivot"
recipe: "blackarch"
source: "https://github.com/0x36/VPNPivot"
license: "custom:unknown"
blackarch-pkgver: "22.37bbde0"
---

# Installing vpnpivot

> Explore the network using this tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vpnpivot) (version `22.37bbde0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S vpnpivot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/0x36/VPNPivot.git
cd VPNPivot
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `linux-headers`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr --sbindir=/usr/bin

  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[vpnpivot]] · [[BlackArch Tools]]

