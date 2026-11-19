---
tool: "nfex"
build-system: "autotools"
upstream: "https://code.google.com/p/nfex/"
recipe: "blackarch"
source: "https://code.google.com/p/nfex/"
license: "GPL2"
blackarch-pkgver: "2.5"
---

# Installing nfex

> A tool for extracting files from the network in real-time or post-capture from an offline tcpdump pcap savefile.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nfex) (version `2.5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S nfex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://code.google.com/p/nfex/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `subversion`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[nfex]] · [[BlackArch Tools]]

