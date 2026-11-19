---
tool: "sslsniff"
build-system: "autotools"
upstream: "https://github.com/moxie0/sslsniff"
recipe: "blackarch"
aur-package: "sslsniff"
source: "https://github.com/moxie0/sslsniff"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.8"
kali-package: "sslsniff"
kali-version: "0.8+git20111017-4+b1"
---

# Installing sslsniff

> A tool to MITM all SSL connections on a LAN and dynamically generate certs for the domains that are being accessed on the fly.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sslsniff) (version `0.8`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sslsniff    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sslsniff    # Kali package version 0.8+git20111017-4+b1
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/moxie0/sslsniff) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `dsniff`
  - `log4cpp`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr --disable-dependency-tracking
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/sslsniff`

Backlinks: [[sniffer]] · [[sslsniff]] · [[BlackArch Tools]]

