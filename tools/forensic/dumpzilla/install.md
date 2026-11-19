---
tool: "dumpzilla"
build-system: "scripts"
upstream: "http://www.dumpzilla.org/"
recipe: "blackarch"
source: "http://www.dumpzilla.org/dumpzilla.py"
license: "GPL3"
blackarch-pkgver: "03152013"
kali-package: "dumpzilla"
kali-version: "20210311-0kali4"
---

# Installing dumpzilla

> A forensic tool for firefox.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dumpzilla) (version `03152013`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dumpzilla    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dumpzilla    # Kali package version 20210311-0kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.dumpzilla.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pysqlite3`, `simplejson`
- **System libraries** (Arch name — translate for your distro):
  - `nss` — Debian libnss3-dev / Fedora nss-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dumpzilla]] · [[BlackArch Tools]]

