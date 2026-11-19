---
tool: "apkstat"
build-system: "scripts"
upstream: "https://github.com/hexabin/APKStat"
recipe: "blackarch"
source: "https://github.com/hexabin/APKStat"
license: "custom:unknown"
blackarch-pkgver: "18.81cdad3"
---

# Installing apkstat

> Automated Information Retrieval From APKs For Initial Analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apkstat) (version `18.81cdad3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S apkstat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hexabin/APKStat.git
cd APKStat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-apktool`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[apkstat]] · [[BlackArch Tools]]

