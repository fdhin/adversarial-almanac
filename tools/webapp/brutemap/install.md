---
tool: "brutemap"
build-system: "scripts"
upstream: "https://github.com/brutemap-dev/brutemap"
recipe: "blackarch"
source: "https://github.com/brutemap-dev/brutemap"
license: "GPL3"
blackarch-pkgver: "65.da4b303"
---

# Installing brutemap

> Penetration testing tool that automates testing accounts to the site's login page.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brutemap) (version `65.da4b303`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S brutemap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/brutemap-dev/brutemap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `geckodriver`
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-backports.shutil_get_terminal_size, python2-colorama, python2-requests, python2-selenium, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[brutemap]] · [[BlackArch Tools]]

