---
tool: "mft2csv"
build-system: "scripts"
upstream: "https://github.com/jschicht/Mft2Csv"
recipe: "blackarch"
source: "https://github.com/jschicht/Mft2Csv"
license: "CCPL"
blackarch-pkgver: "40.164eb224"
---

# Installing mft2csv

> Extract $MFT record info and log it to a csv file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mft2csv) (version `40.164eb224`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S mft2csv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/Mft2Csv.git
cd Mft2Csv
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

Backlinks: [[forensic]] · [[mft2csv]] · [[BlackArch Tools]]

