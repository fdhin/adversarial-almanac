---
tool: "ctunnel"
build-system: "make"
upstream: "http://nardcore.org/ctunnel"
recipe: "blackarch"
source: "http://nardcore.org/ctunnel"
license: "GPL"
blackarch-pkgver: "0.7"
---

# Installing ctunnel

> Tunnel and/or proxy TCP or UDP connections via a cryptographic tunnel.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ctunnel) (version `0.7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ctunnel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://nardcore.org/ctunnel) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libgcrypt`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[tunnel]] · [[ctunnel]] · [[BlackArch Tools]]

