---
tool: "bulk-extractor"
build-system: "autotools"
upstream: "https://github.com/simsong/bulk_extractor"
recipe: "blackarch"
source: "https://github.com/simsong/bulk_extractor"
license: "GPL"
blackarch-pkgver: "2898.e8638394"
---

# Installing bulk-extractor

> Bulk Email and URL extraction tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bulk-extractor) (version `2898.e8638394`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bulk-extractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/simsong/bulk_extractor) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `afflib`
  - `exiv2`
  - `libewf`
  - `ncurses5-compat-libs`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `tre`

## Exact build steps from the recipe

```bash
cd ${pkgname//-/_}

  ./configure --prefix=/usr --sysconfdir=/etc

  make

  make -C java_gui
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[bulk-extractor]] · [[BlackArch Tools]]

