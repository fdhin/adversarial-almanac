---
tool: "fastnetmon"
build-system: "cmake"
upstream: "https://github.com/pavel-odintsov/fastnetmon"
recipe: "blackarch"
source: "https://github.com/pavel-odintsov/fastnetmon"
license: "GPL"
blackarch-pkgver: "v1.2.9.r70.g1f285649"
---

# Installing fastnetmon

> High performance DoS/DDoS load analyzer built on top of multiple packet capture engines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fastnetmon) (version `v1.2.9.r70.g1f285649`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S fastnetmon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/pavel-odintsov/fastnetmon) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `binutils`
  - `boost`
  - `boost-libs`
  - `capnproto`
  - `grpc`
  - `hiredis`
  - `json-c`
  - `libbpf`
  - `libpfring`
  - `log4cpp`
  - `luajit`
  - `mongo-c-driver`
  - `ndpi`
  - `numactl`
  - `pfring-dkms`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  mkdir -p build
  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[fastnetmon]] · [[BlackArch Tools]]

