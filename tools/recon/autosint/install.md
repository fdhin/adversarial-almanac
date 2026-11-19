---
tool: "autosint"
build-system: "scripts"
upstream: "https://github.com/bharshbarger/AutOSINT"
recipe: "blackarch"
source: "https://github.com/bharshbarger/AutOSINT"
license: "GPL2"
blackarch-pkgver: "236.25d292c"
---

# Installing autosint

> Tool to automate common osint tasks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autosint) (version `236.25d292c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S autosint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/bharshbarger/AutOSINT.git
cd AutOSINT
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-google, python2-python-docx, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[autosint]] · [[BlackArch Tools]]

