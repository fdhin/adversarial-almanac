---
tool: "exescan"
build-system: "scripts"
upstream: "https://github.com/cysinfo/Exescan"
recipe: "blackarch"
source: "https://github.com/cysinfo/Exescan"
license: "custom:unknown"
blackarch-pkgver: "1.ad993e3"
---

# Installing exescan

> A tool to detect anomalies in PE (Portable Executable) files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exescan) (version `1.ad993e3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S exescan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/cysinfo/Exescan.git
cd Exescan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pefile (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[exescan]] · [[BlackArch Tools]]

