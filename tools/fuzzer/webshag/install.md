---
tool: "webshag"
build-system: "scripts"
upstream: "http://www.scrt.ch/en/attack/downloads/webshag"
recipe: "blackarch"
source: "http://www.scrt.ch/en/attack/downloads/webshag"
license: "GPL3"
blackarch-pkgver: "1.10"
provides: ["webshag_cli", "webshag_gui"]
---

# Installing webshag

> A multi-threaded, multi-platform web server audit tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webshag) (version `1.10`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webshag    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.scrt.ch/en/attack/downloads/webshag) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `nikto`
  - `nmap`
  - `python2`
  - `wxpython`

## Installed layout (per recipe)

- Commands: `/usr/bin/webshag_cli`, `/usr/bin/webshag_gui`

Backlinks: [[fuzzer]] · [[webshag]] · [[BlackArch Tools]]

