---
tool: "videosnarf"
build-system: "autotools"
upstream: "https://ucsniff.sourceforge.net/videosnarf.html"
recipe: "blackarch"
aur-package: "videosnarf"
source: "https://ucsniff.sourceforge.net/videosnarf.html"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.63"
---

# Installing videosnarf

> A new security assessment tool for pcap analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/videosnarf) (version `0.63`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S videosnarf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://ucsniff.sourceforge.net/videosnarf.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  patch -Np1 -i ../videosnarf-0.63-fix-linking.patch

  autoreconf -fi

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[videosnarf]] · [[BlackArch Tools]]

