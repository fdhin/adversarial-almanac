---
tool: "wnmap"
build-system: "scripts"
upstream: "http://nullsecurity.net/tools/automation.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/automation.html"
license: "GPL3"
blackarch-pkgver: "0.1"
---

# Installing wnmap

> A shell script written with the purpose to automate and chain scans via nmap.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wnmap) (version `0.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wnmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://nullsecurity.net/tools/automation.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `vim`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[wnmap]] · [[BlackArch Tools]]

