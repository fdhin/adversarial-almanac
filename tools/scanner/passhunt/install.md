---
tool: "passhunt"
build-system: "scripts"
upstream: "https://github.com/Dionach/PassHunt"
recipe: "blackarch"
source: "https://github.com/Dionach/PassHunt"
license: "GPL3"
blackarch-pkgver: "5.332f374"
---

# Installing passhunt

> Search drives for documents containing passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/passhunt) (version `5.332f374`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S passhunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Dionach/PassHunt.git
cd PassHunt
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-progressbar (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[passhunt]] · [[BlackArch Tools]]

