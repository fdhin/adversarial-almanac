---
tool: "passivedns"
build-system: "autotools"
upstream: "https://github.com/gamelinux/passivedns"
recipe: "blackarch"
source: "https://github.com/gamelinux/passivedns"
license: "unknown"
blackarch-pkgver: "322.95411ba"
---

# Installing passivedns

> A network sniffer that logs all DNS server replies for use in a passive DNS setup.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/passivedns) (version `322.95411ba`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S passivedns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/gamelinux/passivedns) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `ldns`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[passivedns]] · [[BlackArch Tools]]

