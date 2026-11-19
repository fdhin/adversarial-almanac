---
tool: "ipaudit"
build-system: "autotools"
upstream: "http://ipaudit.sourceforge.net"
recipe: "blackarch"
source: "http://ipaudit.sourceforge.net"
license: "GPL"
blackarch-pkgver: "1.1"
---

# Installing ipaudit

> Monitors network activity on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipaudit) (version `1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ipaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://ipaudit.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[ipaudit]] · [[BlackArch Tools]]

