---
tool: "dns-spoof"
build-system: "make"
upstream: "https://github.com/maurotfilho/dns-spoof"
recipe: "blackarch"
source: "https://github.com/maurotfilho/dns-spoof"
license: "GPL"
blackarch-pkgver: "13.81ba29f"
---

# Installing dns-spoof

> Yet another DNS spoof utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dns-spoof) (version `13.81ba29f`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dns-spoof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/maurotfilho/dns-spoof) (the recipe uses a versioned archive).

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

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[dns-spoof]] · [[BlackArch Tools]]

