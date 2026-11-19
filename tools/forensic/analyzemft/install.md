---
tool: "analyzemft"
build-system: "python"
upstream: "https://github.com/dkovar/analyzeMFT"
recipe: "blackarch"
source: "https://github.com/dkovar/analyzeMFT"
license: "CPL"
blackarch-pkgver: "v3.1.1.0.r0.gd82bc98"
---

# Installing analyzemft

> Parse the MFT file from an NTFS filesystem.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/analyzemft) (version `v3.1.1.0.r0.gd82bc98`, license CPL).

## Arch / BlackArch

```bash
sudo pacman -S analyzemft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/dkovar/analyzeMFT.git
cd analyzeMFT
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `openpyxl`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[analyzemft]] · [[BlackArch Tools]]

