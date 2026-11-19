---
tool: "sslmap"
build-system: "scripts"
upstream: "http://thesprawl.org/projects/latest/"
recipe: "blackarch"
source: "http://thesprawl.org/projects/latest/"
license: "unknown"
blackarch-pkgver: "0.2.0"
---

# Installing sslmap

> A lightweight TLS/SSL cipher suite scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sslmap) (version `0.2.0`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S sslmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://thesprawl.org/projects/latest/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sslmap]] · [[BlackArch Tools]]

