---
tool: "zykeys"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/119156/Zykeys-Wireless-Tool.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119156/Zykeys-Wireless-Tool.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing zykeys

> Demonstrates how default wireless settings are derived on some models of ZyXEL routers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zykeys) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zykeys    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/119156/Zykeys-Wireless-Tool.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc "$pkgname.c" -o $pkgname -lcrypto
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[zykeys]] · [[BlackArch Tools]]

