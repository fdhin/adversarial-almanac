---
tool: "cvechecker"
build-system: "autotools"
upstream: "https://github.com/sjvermeu/cvechecker"
recipe: "blackarch"
aur-package: "cvechecker"
source: "https://github.com/sjvermeu/cvechecker"
license: "GPL-3.0-only"
blackarch-pkgver: "4.0"
---

# Installing cvechecker

> The goal of cvechecker is to report about possible vulnerabilities on your system, by scanning the installed software and matching the results with the CVE database.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cvechecker) (version `4.0`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S cvechecker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/sjvermeu/cvechecker) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`
- **System libraries** (Arch name — translate for your distro):
  - `libbsd`
  - `libconfig`
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `sqlite3`
  - `wget`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
    --includedir=/usr/include --enable-sqlite3

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cvechecker]] · [[BlackArch Tools]]

