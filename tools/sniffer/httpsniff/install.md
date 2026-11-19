---
tool: "httpsniff"
build-system: "make"
upstream: "https://www.sump.org/projects/httpsniff/"
recipe: "blackarch"
source: "https://www.sump.org/projects/httpsniff/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "0.4"
---

# Installing httpsniff

> Tool to sniff HTTP responses from TCP/IP based networks and save contained files locally for later review.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpsniff) (version `0.4`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S httpsniff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.sump.org/projects/httpsniff/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glib2`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libnids`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
make clean
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[httpsniff]] · [[BlackArch Tools]]

