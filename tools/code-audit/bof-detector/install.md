---
tool: "bof-detector"
build-system: "scripts"
upstream: "https://github.com/st9140927/BOF_Detector"
recipe: "blackarch"
source: "https://github.com/st9140927/BOF_Detector"
license: "custom:unknown"
blackarch-pkgver: "19.e08367d"
---

# Installing bof-detector

> A simple detector of BOF vulnerabilities by source-code-level check.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bof-detector) (version `19.e08367d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bof-detector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/st9140927/BOF_Detector.git
cd BOF_Detector
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[bof-detector]] · [[BlackArch Tools]]

