---
tool: "pemcrack"
build-system: "other"
upstream: "https://github.com/robertdavidgraham/pemcrack"
recipe: "blackarch"
source: "https://github.com/robertdavidgraham/pemcrack"
license: "custom:unknown"
blackarch-pkgver: "12.66e02b8"
---

# Installing pemcrack

> Cracks SSL PEM files that hold encrypted private keys. Brute forces or dictionary cracks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pemcrack) (version `12.66e02b8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pemcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/robertdavidgraham/pemcrack) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc "$pkgname.c" -lssl -lcrypto -Wall -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[pemcrack]] · [[BlackArch Tools]]

