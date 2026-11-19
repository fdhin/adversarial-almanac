---
tool: "tcptrace"
build-system: "make"
upstream: "http://tcptrace.org/"
recipe: "blackarch"
aur-package: "tcptrace"
source: "http://tcptrace.org/"
license: "GPL"
blackarch-pkgver: "6.6.7"
provides: ["xpl2gpl"]
---

# Installing tcptrace

> A TCP dump file analysis tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcptrace) (version `6.6.7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tcptrace    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://tcptrace.org/) (the recipe uses a versioned archive).

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

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/xpl2gpl`
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[tcptrace]] · [[BlackArch Tools]]

