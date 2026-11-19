---
tool: "haka"
build-system: "cmake"
upstream: "https://github.com/haka-security/haka"
recipe: "blackarch"
source: "https://github.com/haka-security/haka"
license: "Mozilla2"
blackarch-pkgver: "v0.3.0.r222.g37ae3090"
---

# Installing haka

> A collection of tool that allows capturing TCP/IP packets and filtering them based on Lua policy files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/haka) (version `v0.3.0.r222.g37ae3090`, license Mozilla2).

## Arch / BlackArch

```bash
sudo pacman -S haka    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/haka-security/haka) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `doxygen`
  - `gawk`
  - `geoip`
  - `iniparser`
  - `libedit`
  - `libnetfilter_queue`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `lua`
  - `ncurses`
  - `pcre`
  - `rsync`
  - `swig`
  - `wireshark-cli`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p make

  cd make

  cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[haka]] · [[BlackArch Tools]]

