---
tool: "h2buster"
build-system: "scripts"
upstream: "https://github.com/00xc/h2buster"
recipe: "blackarch"
source: "https://github.com/00xc/h2buster"
license: "GPL"
blackarch-pkgver: "79.6c4dd1c"
---

# Installing h2buster

> A threaded, recursive, web directory brute-force scanner over HTTP/2.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/h2buster) (version `79.6c4dd1c`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S h2buster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/00xc/h2buster) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `hyper`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[h2buster]] · [[BlackArch Tools]]

