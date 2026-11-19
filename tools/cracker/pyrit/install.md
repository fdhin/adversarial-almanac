---
tool: "pyrit"
build-system: "other"
upstream: "https://github.com/JPaulMora/Pyrit"
recipe: "blackarch"
aur-package: "pyrit"
source: "https://github.com/JPaulMora/Pyrit"
license: "GPL3"
blackarch-pkgver: "0.5.0"
---

# Installing pyrit

> The famous WPA precomputed cracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyrit) (version `0.5.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pyrit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/JPaulMora/Pyrit) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python2`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pyrit]] · [[BlackArch Tools]]

