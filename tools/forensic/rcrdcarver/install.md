---
tool: "rcrdcarver"
build-system: "scripts"
upstream: "https://github.com/jschicht/RcrdCarver"
recipe: "blackarch"
source: "https://github.com/jschicht/RcrdCarver"
license: "CCPL"
blackarch-pkgver: "5.54507d21"
---

# Installing rcrdcarver

> Carve RCRD records ($LogFile) from a chunk of data..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rcrdcarver) (version `5.54507d21`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S rcrdcarver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/RcrdCarver.git
cd RcrdCarver
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[forensic]] · [[rcrdcarver]] · [[BlackArch Tools]]

