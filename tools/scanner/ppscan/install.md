---
tool: "ppscan"
build-system: "other"
upstream: "https://packetstormsecurity.com/files/82897/PPScan-Portscanner-0.3.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/82897/PPScan-Portscanner-0.3.html"
license: "custom:unknown"
blackarch-pkgver: "0.3"
---

# Installing ppscan

> Yet another port scanner with HTTP and FTP tunneling support.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ppscan) (version `0.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ppscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/82897/PPScan-Portscanner.3.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  gcc -o $pkgname "$pkgname.c" -lpthread
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ppscan]] · [[BlackArch Tools]]

