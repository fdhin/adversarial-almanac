---
tool: "pkt2flow"
build-system: "other"
upstream: "https://github.com/caesar0301/pkt2flow"
recipe: "blackarch"
source: "https://github.com/caesar0301/pkt2flow"
license: "MIT"
blackarch-pkgver: "82.e4cd57b"
---

# Installing pkt2flow

> A simple utility to classify packets into flows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pkt2flow) (version `82.e4cd57b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pkt2flow    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/caesar0301/pkt2flow) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `scons`

## Exact build steps from the recipe

```bash
cd $pkgname

  scons -Q PREFIX=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[pkt2flow]] · [[BlackArch Tools]]

