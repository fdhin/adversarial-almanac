---
tool: "gwcheck"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/62047/gwcheck.c.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/gwcheck.c"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing gwcheck

> A simple program that checks if a host in an ethernet network is a gateway to Internet.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gwcheck) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S gwcheck    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/62047/gwcheck.c.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
gcc -O2 "$pkgname.c" -o $pkgname -lpcap
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[gwcheck]] · [[BlackArch Tools]]

