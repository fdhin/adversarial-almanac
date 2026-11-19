---
tool: "wlan2eth"
build-system: "make"
upstream: "https://www.willhackforsushi.com/resources/wlan2eth/"
recipe: "blackarch"
source: "https://www.willhackforsushi.com/resources/wlan2eth/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.3"
---

# Installing wlan2eth

> Re-writes 802.11 captures into standard Ethernet frames.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wlan2eth) (version `1.3`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S wlan2eth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://www.willhackforsushi.com/resources/wlan2eth/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  sed -i "s|-g3 -ggdb -pipe|$CFLAGS|g" Makefile

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wlan2eth]] · [[BlackArch Tools]]

