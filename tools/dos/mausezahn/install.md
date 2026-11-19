---
tool: "mausezahn"
build-system: "cmake"
upstream: "http://www.perihel.at/sec/mz/"
recipe: "blackarch"
source: "http://www.perihel.at/sec/mz/"
license: "GPL"
blackarch-pkgver: "0.40"
---

# Installing mausezahn

> A free fast traffic generator written in C which allows you to send nearly every possible and impossible packet.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mausezahn) (version `0.40`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S mausezahn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.perihel.at/sec/mz/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `libcli`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake . -DCMAKE_INSTALL_PREFIX=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/mz`

Backlinks: [[dos]] · [[mausezahn]] · [[BlackArch Tools]]

