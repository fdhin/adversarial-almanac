---
tool: "hashdb"
build-system: "autotools"
upstream: "https://github.com/NPS-DEEP/hashdb/"
recipe: "blackarch"
aur-package: "hashdb"
source: "https://github.com/NPS-DEEP/hashdb/"
license: "GPL"
blackarch-pkgver: "1089.1da1b9f"
---

# Installing hashdb

> A block hash toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hashdb) (version `1089.1da1b9f`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hashdb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/NPS-DEEP/hashdb/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libewf`
  - `libtool`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python2`
  - `tre`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./bootstrap.sh

  sed -i s/SSL_library_init/OPENSSL_init_ssl/ configure.ac configure

  ./configure --prefix=/usr --with-openssl

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[hashdb]] · [[BlackArch Tools]]

