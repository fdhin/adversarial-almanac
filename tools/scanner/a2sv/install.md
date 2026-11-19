---
tool: "a2sv"
build-system: "scripts"
upstream: "https://github.com/hahwul/a2sv"
recipe: "blackarch"
source: "https://github.com/hahwul/a2sv"
license: "MIT"
blackarch-pkgver: "140.cb24c4e"
---

# Installing a2sv

> Auto Scanning to SSL Vulnerability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/a2sv) (version `140.cb24c4e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S a2sv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hahwul/a2sv) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python2`
- ⚠️ Python 2 only deps: python2-netaddr (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[a2sv]] · [[BlackArch Tools]]

