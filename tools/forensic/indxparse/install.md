---
tool: "indxparse"
build-system: "scripts"
upstream: "http://www.williballenthin.com/forensics/mft/indxparse/"
recipe: "blackarch"
source: "http://www.williballenthin.com/forensics/mft/indxparse/"
license: "APACHE"
blackarch-pkgver: "331.038e8ec"
---

# Installing indxparse

> A Tool suite for inspecting NTFS artifacts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/indxparse) (version `331.038e8ec`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S indxparse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/williballenthin/INDXParse.git
cd INDXParse
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-progressbar (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[indxparse]] · [[BlackArch Tools]]

