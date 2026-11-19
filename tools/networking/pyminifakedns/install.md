---
tool: "pyminifakedns"
build-system: "scripts"
upstream: "http://code.activestate.com/recipes/491264/"
recipe: "blackarch"
source: "http://code.activestate.com/recipes/491264-mini-fake-dns-server/download/1"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing pyminifakedns

> Minimal DNS server written in Python; it always replies with a 127.0.0.1 A-record.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyminifakedns) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pyminifakedns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.activestate.com/recipes/491264/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[pyminifakedns]] · [[BlackArch Tools]]

