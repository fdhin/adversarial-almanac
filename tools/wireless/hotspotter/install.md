---
tool: "hotspotter"
build-system: "make"
upstream: "http://www.remote-exploit.org/?page_id=418"
recipe: "blackarch"
source: "http://www.remote-exploit.org/?page_id=418"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.5"
---

# Installing hotspotter

> Passively monitor the network for probe request frames to identify the preferred networks of Windows XP clients, and compare it to a supplied list of common hotspot network names.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hotspotter) (version `0.5`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S hotspotter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.remote-exploit.org/?page_id=418) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[hotspotter]] · [[BlackArch Tools]]

