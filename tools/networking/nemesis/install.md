---
tool: "nemesis"
build-system: "make"
upstream: "https://github.com/troglobit/nemesis"
recipe: "blackarch"
aur-package: "nemesis"
source: "https://github.com/troglobit/nemesis"
license: "BSD"
blackarch-pkgver: "331.5aee868"
---

# Installing nemesis

> A command-line network packet crafting and injection utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nemesis) (version `331.5aee868`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S nemesis    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/troglobit/nemesis) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[nemesis]] · [[BlackArch Tools]]

