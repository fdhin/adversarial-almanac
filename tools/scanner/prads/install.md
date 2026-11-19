---
tool: "prads"
build-system: "make"
upstream: "http://gamelinux.github.io/prads/"
recipe: "blackarch"
source: "http://gamelinux.github.io/prads/"
license: "PerlArtistic"
blackarch-pkgver: "1134.81334aa"
---

# Installing prads

> A "Passive Real-time Asset Detection System".

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/prads) (version `1134.81334aa`, license PerlArtistic).

## Arch / BlackArch

```bash
sudo pacman -S prads    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://gamelinux.github.io/prads/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Python (pip)**: `docutils`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make PREFIX=/
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[prads]] · [[BlackArch Tools]]

