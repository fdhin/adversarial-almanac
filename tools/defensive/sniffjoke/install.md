---
tool: "sniffjoke"
build-system: "cmake"
upstream: "http://www.delirandom.net/sniffjoke/"
recipe: "blackarch"
source: "http://www.delirandom.net/sniffjoke/"
license: "GPL"
blackarch-pkgver: "772.434bfb1"
kali-package: "sniffjoke"
kali-version: "0.4.1-1kali2"
---

# Installing sniffjoke

> Injects packets in the transmission flow that are able to seriously disturb passive analysis like sniffing, interception and low level information theft.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sniffjoke) (version `772.434bfb1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sniffjoke    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sniffjoke    # Kali package version 0.4.1-1kali2
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.delirandom.net/sniffjoke/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `iptables`
  - `tcpdump`

## Exact build steps from the recipe

```bash
cd $pkgname

  [[ -d build ]] || mkdir build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[sniffjoke]] · [[BlackArch Tools]]

