---
tool: "cdpsnarf"
build-system: "make"
upstream: "https://github.com/Zapotek/cdpsnarf"
recipe: "blackarch"
source: "https://github.com/Zapotek/cdpsnarf"
license: "GPL-2.0-only"
blackarch-pkgver: "0.1.6"
---

# Installing cdpsnarf

> Cisco discovery protocol sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cdpsnarf) (version `0.1.6`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S cdpsnarf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/Zapotek/cdpsnarf) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[cdpsnarf]] · [[BlackArch Tools]]

