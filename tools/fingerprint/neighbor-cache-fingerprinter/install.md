---
tool: "neighbor-cache-fingerprinter"
build-system: "autotools"
upstream: "https://github.com/PherricOxide/Neighbor-Cache-Fingerprinter"
recipe: "blackarch"
source: "https://github.com/PherricOxide/Neighbor-Cache-Fingerprinter"
license: "GPL3"
blackarch-pkgver: "83.f1e596f"
provides: ["neighbor-fingerprint"]
---

# Installing neighbor-cache-fingerprinter

> An ARP based Operating System version scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/neighbor-cache-fingerprinter) (version `83.f1e596f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S neighbor-cache-fingerprinter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/PherricOxide/Neighbor-Cache-Fingerprinter.git
cd Neighbor-Cache-Fingerprinter
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr --with-boost

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/neighbor-fingerprint`

Backlinks: [[fingerprint]] · [[neighbor-cache-fingerprinter]] · [[BlackArch Tools]]

