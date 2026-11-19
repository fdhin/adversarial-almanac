---
tool: "smtpmap"
build-system: "autotools"
upstream: "http://www.projectiwear.org/~plasmahh/software.html"
recipe: "blackarch"
source: "http://www.projectiwear.org/~plasmahh/software.html"
license: "GPL"
blackarch-pkgver: "0.8.234_BETA"
---

# Installing smtpmap

> Tool to identify the running smtp software on a given host.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtpmap) (version `0.8.234_BETA`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S smtpmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.projectiwear.org/~plasmahh/software.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-${pkgver/_/-}"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[smtpmap]] · [[BlackArch Tools]]

