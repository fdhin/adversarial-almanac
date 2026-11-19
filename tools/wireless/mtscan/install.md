---
tool: "mtscan"
build-system: "cmake"
upstream: "https://github.com/kkonradpl/mtscan"
recipe: "blackarch"
source: "https://github.com/kkonradpl/mtscan"
license: "GPL2"
blackarch-pkgver: "176.dd44f0e"
---

# Installing mtscan

> Mikrotik RouterOS wireless scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mtscan) (version `176.dd44f0e`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mtscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/kkonradpl/mtscan) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gtk2`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libssh`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `yajl`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[mtscan]] · [[BlackArch Tools]]

