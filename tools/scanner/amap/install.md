---
tool: "amap"
build-system: "autotools"
upstream: "http://freeworld.thc.org/releases.php"
recipe: "blackarch"
source: "https://github.com/BlackArch/amap.git"
license: "GPL-2.0-or-later, custom"
blackarch-pkgver: "5.4"
provides: ["amapcrap"]
---

# Installing amap

> Next-generation tool for assisting network penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/amap) (version `5.4`, license GPL-2.0-or-later, custom).

## Arch / BlackArch

```bash
sudo pacman -S amap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/BlackArch/amap.git
cd amap
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ca-certificates`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr/share/amap/

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/amapcrap`
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[amap]] · [[BlackArch Tools]]

