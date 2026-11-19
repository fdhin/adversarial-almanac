---
tool: "lfle"
build-system: "scripts"
upstream: "https://github.com/williballenthin/LfLe"
recipe: "blackarch"
source: "https://github.com/williballenthin/LfLe"
license: "custom:unknown"
blackarch-pkgver: "24.f28592c"
---

# Installing lfle

> Recover event log entries from an image by heurisitically looking for record structures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfle) (version `24.f28592c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lfle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/williballenthin/LfLe.git
cd LfLe
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-python-evt (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[lfle]] · [[BlackArch Tools]]

