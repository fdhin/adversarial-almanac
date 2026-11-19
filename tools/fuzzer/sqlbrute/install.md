---
tool: "sqlbrute"
build-system: "scripts"
upstream: "http://www.justinclarke.com/archives/2006/03/sqlbrute.html"
recipe: "blackarch"
source: "http://www.justinclarke.com/archives/2006/03/sqlbrute.html"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing sqlbrute

> Brute forces data out of databases using blind SQL injection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqlbrute) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sqlbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.justinclarke.com/archives/2006/03/sqlbrute.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[sqlbrute]] · [[BlackArch Tools]]

