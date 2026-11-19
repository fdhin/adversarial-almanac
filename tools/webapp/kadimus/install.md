---
tool: "kadimus"
build-system: "make"
upstream: "https://github.com/P0cL4bs/Kadimus"
recipe: "blackarch"
source: "https://github.com/P0cL4bs/Kadimus"
license: "MIT"
blackarch-pkgver: "183.ac5f438"
---

# Installing kadimus

> LFI Scan & Exploit Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kadimus) (version `183.ac5f438`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S kadimus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/P0cL4bs/Kadimus.git
cd Kadimus
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `libssh`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[kadimus]] · [[BlackArch Tools]]

