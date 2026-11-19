---
tool: "indx2csv"
build-system: "scripts"
upstream: "https://github.com/jschicht/Indx2Csv"
recipe: "blackarch"
source: "https://github.com/jschicht/Indx2Csv"
license: "CCPL"
blackarch-pkgver: "17.129a411e"
---

# Installing indx2csv

> An advanced parser for INDX records.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/indx2csv) (version `17.129a411e`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S indx2csv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/Indx2Csv.git
cd Indx2Csv
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

Backlinks: [[forensic]] · [[indx2csv]] · [[BlackArch Tools]]

