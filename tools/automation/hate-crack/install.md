---
tool: "hate-crack"
build-system: "scripts"
upstream: "https://github.com/trustedsec/hate_crack"
recipe: "blackarch"
source: "https://github.com/trustedsec/hate_crack"
license: "custom:unknown"
blackarch-pkgver: "1350.d6eb5d5"
---

# Installing hate-crack

> A tool for automating cracking methodologies through Hashcat.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hate-crack) (version `1350.d6eb5d5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hate-crack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/trustedsec/hate_crack.git
cd hate_crack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pyenchant`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `hashcat`
  - `pygtk`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[hate-crack]] · [[BlackArch Tools]]

