---
tool: "tcpdstat"
build-system: "make"
upstream: "https://github.com/netik/tcpdstat"
recipe: "blackarch"
source: "https://github.com/netik/tcpdstat"
license: "custom:unknown"
blackarch-pkgver: "4.be5bd28"
---

# Installing tcpdstat

> Get protocol statistics from tcpdump pcap files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpdstat) (version `4.be5bd28`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tcpdstat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/netik/tcpdstat) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[tcpdstat]] · [[BlackArch Tools]]

