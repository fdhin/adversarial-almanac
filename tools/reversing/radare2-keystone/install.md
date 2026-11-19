---
tool: "radare2-keystone"
build-system: "autotools"
upstream: "https://github.com/radare/radare2-extras/tree/master/unicorn"
recipe: "blackarch"
source: "https://github.com/radare/radare2-extras/tree/master/unicorn"
license: "GPL3"
blackarch-pkgver: "960.68e9f6d"
---

# Installing radare2-keystone

> Keystone assembler plugins for radare2.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/radare2-keystone) (version `960.68e9f6d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S radare2-keystone    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/radare/radare2-extras.git
cd radare2-extras
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `jansson`
  - `keystone`
  - `linux-headers`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pkg-config`
  - `radare2`
  - `unicorn`
  - `yara`

## Exact build steps from the recipe

```bash
cd $pkgname

  cd keystone
  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/radare2-extras`

Backlinks: [[reversing]] · [[radare2-keystone]] · [[BlackArch Tools]]

