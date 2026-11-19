---
tool: "dbusmap"
build-system: "make"
upstream: "https://github.com/taviso/dbusmap"
recipe: "blackarch"
source: "https://github.com/taviso/dbusmap"
license: "GPL2"
blackarch-pkgver: "16.6bb2831"
---

# Installing dbusmap

> Simple utility for enumerating D-Bus endpoints, an nmap for D-Bus.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dbusmap) (version `16.6bb2831`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dbusmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/taviso/dbusmap) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `procps-ng`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dbusmap]] · [[BlackArch Tools]]

