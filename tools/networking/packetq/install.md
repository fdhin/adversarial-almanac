---
tool: "packetq"
build-system: "autotools"
upstream: "https://github.com/DNS-OARC/PacketQ"
recipe: "blackarch"
source: "https://github.com/DNS-OARC/PacketQ"
license: "GPL3"
blackarch-pkgver: "311.9ebf537"
---

# Installing packetq

> A tool that provides a basic SQL-frontend to PCAP-files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/packetq) (version `311.9ebf537`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S packetq    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/DNS-OARC/PacketQ.git
cd PacketQ
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/packetq`

Backlinks: [[networking]] · [[packetq]] · [[BlackArch Tools]]

