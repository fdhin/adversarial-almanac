---
tool: "nbtool"
build-system: "make"
upstream: "http://wiki.skullsecurity.org/Nbtool"
recipe: "blackarch"
source: "http://wiki.skullsecurity.org/Nbtool"
license: "GPL3"
blackarch-pkgver: "2.bf90c76"
---

# Installing nbtool

> Some tools for NetBIOS and DNS investigation, attacks, and communication.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nbtool) (version `2.bf90c76`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nbtool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://wiki.skullsecurity.org/Nbtool) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `nasm`

## Exact build steps from the recipe

```bash
cd $pkgname

  make CFLAGS="$CFLAGS"

  sed -i "s|/usr/local/|$pkgdir/usr/|g" Makefile
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[nbtool]] · [[BlackArch Tools]]

