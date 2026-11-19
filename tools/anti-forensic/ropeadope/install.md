---
tool: "ropeadope"
build-system: "scripts"
upstream: "http://www.highhacksociety.com/"
recipe: "blackarch"
source: "http://www.highhacksociety.com/"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing ropeadope

> A linux log cleaner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ropeadope) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ropeadope    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nullsecuritynet/tools/raw/main/logcleaner/ropeadope/release/ropeadope.py
cd ropeadope.py
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[anti-forensic]] · [[ropeadope]] · [[BlackArch Tools]]

