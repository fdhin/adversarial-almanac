---
tool: "radare2-unicorn"
build-system: "autotools"
upstream: "https://github.com/radare/radare2-extras/tree/master/unicorn"
recipe: "blackarch"
source: "https://github.com/radare/radare2-extras/tree/master/unicorn"
license: "GPL3"
blackarch-pkgver: "960.68e9f6d"
---

# Installing radare2-unicorn

> Unicorn Emulator Plugin for radare2.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/radare2-unicorn) (version `960.68e9f6d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S radare2-unicorn    # needs the [blackarch] repo enabled
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
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pkg-config`
  - `radare2`
  - `unicorn`
  - `yara`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make unicorn

  cd unicorn

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[radare2-unicorn]] · [[BlackArch Tools]]

