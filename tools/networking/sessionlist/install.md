---
tool: "sessionlist"
build-system: "make"
upstream: "http://www.0xrage.com/"
recipe: "blackarch"
source: "http://www.0xrage.com/"
license: "GPL3"
blackarch-pkgver: "6.3efc3b2"
---

# Installing sessionlist

> Sniffer that intents to sniff HTTP packets and attempts to reconstruct interesting authentication data from websites that do not employ proper secure cookie auth.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sessionlist) (version `6.3efc3b2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sessionlist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.0xrage.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `ncurses`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[sessionlist]] · [[BlackArch Tools]]

