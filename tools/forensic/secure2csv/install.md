---
tool: "secure2csv"
build-system: "scripts"
upstream: "https://github.com/jschicht/Secure2Csv"
recipe: "blackarch"
source: "https://github.com/jschicht/Secure2Csv"
license: "CCPL"
blackarch-pkgver: "10.119eefb0"
---

# Installing secure2csv

> Decode security descriptors in $Secure on NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/secure2csv) (version `10.119eefb0`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S secure2csv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/Secure2Csv.git
cd Secure2Csv
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

Backlinks: [[forensic]] · [[secure2csv]] · [[BlackArch Tools]]

