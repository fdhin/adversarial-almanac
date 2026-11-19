---
tool: "issniff"
build-system: "autotools"
upstream: "https://github.com/juphoff/issniff"
recipe: "blackarch"
source: "https://github.com/juphoff/issniff"
license: "custom:unknown"
blackarch-pkgver: "299.98cb3d0"
---

# Installing issniff

> Internet Session Sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/issniff) (version `299.98cb3d0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S issniff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/juphoff/issniff) (the recipe uses a versioned archive).

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

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[sniffer]] · [[issniff]] · [[BlackArch Tools]]

