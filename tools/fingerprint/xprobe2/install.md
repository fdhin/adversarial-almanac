---
tool: "xprobe2"
build-system: "autotools"
upstream: "https://sourceforge.net/apps/mediawiki/xprobe/index.php?title=Main_Page"
recipe: "blackarch"
aur-package: "xprobe2"
source: "https://sourceforge.net/apps/mediawiki/xprobe/index.php?title=Main_Page"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.3"
---

# Installing xprobe2

> An active OS fingerprinting tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xprobe2) (version `0.3`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S xprobe2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/apps/mediawiki/xprobe/index.php?title=Main_Page) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  patch -p1 < ../ip.patch
  patch -p1 < ../misc.patch
  patch -p1 < ../tcp.patch

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]]; then
    ./configure --prefix=/usr --sysconfdir=/etc
  else
    ./configure --build=arm --prefix=/usr --sysconfdir=/etc
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[xprobe2]] · [[BlackArch Tools]]

