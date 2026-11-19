---
tool: "wafp"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/84468/Web-Application-Finger-Printer-0.01-26c3.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/84468/Web-Application-Finger-Printer-0.01-26c3.html"
license: "unknown"
blackarch-pkgver: "0.01_26c3"
---

# Installing wafp

> An easy to use Web Application Finger Printing tool written in ruby using sqlite3 databases for storing the fingerprints.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wafp) (version `0.01_26c3`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S wafp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/84468/Web-Application-Finger-Printer.01-26c3.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-sqlite3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wafp]] · [[BlackArch Tools]]

