---
tool: "dublin-traceroute"
build-system: "cmake"
upstream: "https://github.com/insomniacslk/dublin-traceroute"
recipe: "blackarch"
nix-package: "dublin-traceroute"
aur-package: "dublin-traceroute"
source: "https://github.com/insomniacslk/dublin-traceroute"
license: "BSD"
blackarch-pkgver: "0.4.2.r176.gd8a9e5b"
---

# Installing dublin-traceroute

> NAT-aware multipath tracerouting tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dublin-traceroute) (version `0.4.2.r176.gd8a9e5b`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S dublin-traceroute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/insomniacslk/dublin-traceroute) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gtest`
  - `jsoncpp`
  - `libcap`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtins`

## Exact build steps from the recipe

```bash
cd "$pkgname"

  mkdir -p build
  cd build
  cmake -DCMAKE_INSTALL_PREFIX=/usr ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dublin-traceroute]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dublin-traceroute    # ad-hoc run, no install
nix profile install nixpkgs#dublin-traceroute   # or persist it
```
