---
tool: "tcpcopy"
build-system: "autotools"
upstream: "https://github.com/session-replay-tools/tcpcopy"
recipe: "blackarch"
source: "https://github.com/session-replay-tools/tcpcopy"
license: "BSD"
blackarch-pkgver: "1285.929511d"
---

# Installing tcpcopy

> A TCP stream replay tool to support real testing of Internet server applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpcopy) (version `1285.929511d`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S tcpcopy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/session-replay-tools/tcpcopy) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr/share/tcpcopy --sbin-path=/usr/share/tcpcopy/tcpcopy

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tcpcopy]] · [[BlackArch Tools]]

