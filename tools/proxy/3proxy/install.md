---
tool: "3proxy"
build-system: "make"
upstream: "https://github.com/3proxy/3proxy"
recipe: "blackarch"
aur-package: "3proxy"
source: "https://github.com/3proxy/3proxy"
license: "Apache-2.0, GPL-2.0-or-later"
blackarch-pkgver: "0.9.6"
provides: ["proxy"]
---

# Installing 3proxy

> Tiny free proxy server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/3proxy) (version `0.9.6`, license Apache-2.0, GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S 3proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/3proxy/3proxy) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make prefix=/usr \
    DESTDIR="$pkgdir" \
    ETCDIR="/etc/$pkgname" \
    INITDIR=/etc/init.d \
    BINDIR=/usr/bin \
    -f Makefile.Linux
```

## Installed layout (per recipe)

- Commands: `/usr/bin/proxy`
- Data: `/usr/share/man`

Backlinks: [[proxy]] · [[3proxy]] · [[BlackArch Tools]]

