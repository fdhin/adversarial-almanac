---
tool: "stompy"
build-system: "make"
upstream: "https://lcamtuf.coredump.cx/"
recipe: "blackarch"
source: "https://lcamtuf.coredump.cx/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.0.4"
---

# Installing stompy

> An advanced utility to test the quality of WWW session identifiers and other tokens that are meant to be unpredictable.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stompy) (version `0.0.4`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S stompy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://lcamtuf.coredump.cx/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[stompy]] · [[BlackArch Tools]]

