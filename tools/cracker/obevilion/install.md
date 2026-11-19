---
tool: "obevilion"
build-system: "scripts"
upstream: "https://github.com/BL4CKvGHOST/Ob3vil1on"
recipe: "blackarch"
source: "https://github.com/BL4CKvGHOST/Ob3vil1on"
license: "GPL3"
blackarch-pkgver: "409.29fbe9d"
---

# Installing obevilion

> Another archive cracker created in python, cracking [zip/7z/rar].

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/obevilion) (version `409.29fbe9d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S obevilion    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/BL4CKvGHOST/Ob3vil1on.git
cd Ob3vil1on
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `p7zip`
  - `python2`
  - `tk`
  - `unrar`
- ⚠️ Python 2 only deps: python2-colorama, python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[obevilion]] · [[BlackArch Tools]]

