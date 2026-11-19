---
tool: "rtpbreak"
build-system: "make"
upstream: "https://packetstorm.news/files/id/66494/"
recipe: "blackarch"
source: "https://packetstorm.news/files/id/66494/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.3a"
kali-package: "rtpbreak"
kali-version: "1.3a-1kali5"
---

# Installing rtpbreak

> Detects, reconstructs and analyzes any RTP session.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtpbreak) (version `1.3a`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rtpbreak    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rtpbreak    # Kali package version 1.3a-1kali5
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://packetstorm.news/files/id/66494/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make CFLAGS="$CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[rtpbreak]] · [[BlackArch Tools]]

