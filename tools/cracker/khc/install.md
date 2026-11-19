---
tool: "khc"
build-system: "make"
upstream: "http://packetstormsecurity.com/files/87003/Known-Host-Cracker-0.2.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/87003/Known-Host-Cracker-0.2.html"
license: "custom:unknown"
blackarch-pkgver: "0.2"
---

# Installing khc

> A small tool designed to recover hashed known_hosts fiels back to their plain-text equivalents.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/khc) (version `0.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S khc    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/87003/Known-Host-Cracker.2.html) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make -f Makefile.gmake
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[khc]] · [[BlackArch Tools]]

