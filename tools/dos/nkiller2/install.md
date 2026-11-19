---
tool: "nkiller2"
build-system: "other"
upstream: "http://sock-raw.org/projects.html"
recipe: "blackarch"
aur-package: "nkiller2"
source: "http://sock-raw.org/projects/nkiller2/nkiller2.c"
license: "GPL3"
blackarch-pkgver: "2.0"
---

# Installing nkiller2

> A TCP exhaustion/stressing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nkiller2) (version `2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nkiller2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sock-raw.org/projects.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
gcc "$pkgname.c" -o $pkgname -lssl -lcrypto -lpcap -O2
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[nkiller2]] · [[BlackArch Tools]]

