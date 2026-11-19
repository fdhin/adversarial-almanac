---
tool: "aimage"
build-system: "autotools"
upstream: "https://pkgs.fedoraproject.org/repo/pkgs/aimage/"
recipe: "blackarch"
source: "https://pkgs.fedoraproject.org/repo/pkgs/aimage/"
license: "custom"
blackarch-pkgver: "3.3.0"
---

# Installing aimage

> A tool to create aff-images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aimage) (version `3.3.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S aimage    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://pkgs.fedoraproject.org/repo/pkgs/aimage/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `afflib`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `readline`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  autoreconf -i
  ./configure --prefix=/usr --sysconfdir=/etc

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[aimage]] · [[BlackArch Tools]]

