---
tool: "fraud-bridge"
build-system: "make"
upstream: "https://github.com/stealth/fraud-bridge"
recipe: "blackarch"
source: "https://github.com/stealth/fraud-bridge"
license: "GPL-3.0-or-later"
blackarch-pkgver: "rel.0.32s.r3.g88a5baa"
---

# Installing fraud-bridge

> ICMP and DNS tunneling via IPv4 and IPv6.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fraud-bridge) (version `rel.0.32s.r3.g88a5baa`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fraud-bridge    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/stealth/fraud-bridge) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libgcrypt`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[fraud-bridge]] · [[BlackArch Tools]]

