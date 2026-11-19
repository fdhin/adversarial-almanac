---
tool: "katsnoop"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/52514/katsnoop.tbz2.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/sniffers/katsnoop.tbz2"
license: "unknown"
blackarch-pkgver: "0.1"
---

# Installing katsnoop

> Utility that sniffs HTTP Basic Authentication information and prints the base64 decoded form.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/katsnoop) (version `0.1`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S katsnoop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/52514/katsnoop.tbz2.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
gcc "$pkgname.c" decode_base64.c -o $pkgname -lpcap
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[katsnoop]] · [[BlackArch Tools]]

