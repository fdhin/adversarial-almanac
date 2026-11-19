---
tool: "rr"
build-system: "cmake"
upstream: "https://github.com/mozilla/rr"
recipe: "blackarch"
nix-package: "rr"
aur-package: "rr"
source: "https://github.com/mozilla/rr"
license: "custom"
blackarch-pkgver: "7333.ca20dcbf"
---

# Installing rr

> A Record and Replay Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rr) (version `7333.ca20dcbf`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S rr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/mozilla/rr) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Python (pip)**: `pexpect`
- **Build tools**: `cmake`, `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `capnproto`
  - `gcc-libs`
  - `gdb`
  - `lib32-zlib`
  - `libcap`
  - `libcap-ng`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpfm4`
  - `man-pages`
  - `python2`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  export CFLAGS="$CFLAGS -fPIC"

  mkdir -p build

  cd build

  cmake -Ddisable32bit=ON -DCMAKE_VERBOSE_MAKEFILE=ON \
    -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr \
    -DPYTHON_EXECUTABLE=/usr/bin/python3 ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[debugger]] · [[rr]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#rr    # ad-hoc run, no install
nix profile install nixpkgs#rr   # or persist it
```
