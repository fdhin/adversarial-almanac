---
tool: "usnjrnl2csv"
build-system: "scripts"
upstream: "https://github.com/jschicht/UsnJrnl2Csv"
recipe: "blackarch"
source: "https://github.com/jschicht/UsnJrnl2Csv"
license: "CCPL"
blackarch-pkgver: "29.1ecbddc"
---

# Installing usnjrnl2csv

> Parser for $UsnJrnl on NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/usnjrnl2csv) (version `29.1ecbddc`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S usnjrnl2csv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/UsnJrnl2Csv.git
cd UsnJrnl2Csv
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

Backlinks: [[forensic]] · [[usnjrnl2csv]] · [[BlackArch Tools]]

