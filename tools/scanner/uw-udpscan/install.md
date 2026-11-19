---
tool: "uw-udpscan"
build-system: "make"
upstream: "http://uberwall.org/bin/download/44/UWudpscan.tar.gz"
recipe: "blackarch"
source: "http://uberwall.org/bin/download/download/44/UWudpscan.tar.gz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing uw-udpscan

> Multi threaded udp scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uw-udpscan) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uw-udpscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

```bash
curl -fLO http://uberwall.org/bin/download/download/44/UWudpscan.tar.gz
tar xf UWudpscan.tar.gz
cd UWudpscan
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "UWudpscan-xmas-release-public"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[uw-udpscan]] · [[BlackArch Tools]]

