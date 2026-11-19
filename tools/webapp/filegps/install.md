---
tool: "filegps"
build-system: "scripts"
upstream: "https://github.com/0blio/fileGPS"
recipe: "blackarch"
source: "https://github.com/0blio/fileGPS"
license: "GPL3"
blackarch-pkgver: "90.03cbc75"
---

# Installing filegps

> A tool that help you to guess how your shell was renamed after the server-side script of the file uploader saved it.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/filegps) (version `90.03cbc75`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S filegps    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0blio/fileGPS.git
cd fileGPS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[filegps]] · [[BlackArch Tools]]

