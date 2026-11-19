---
tool: "mftrcrd"
build-system: "scripts"
upstream: "https://github.com/jschicht/MftRcrd"
recipe: "blackarch"
source: "https://github.com/jschicht/MftRcrd"
license: "CCPL"
blackarch-pkgver: "16.35c3ac2f"
---

# Installing mftrcrd

> Command line $MFT record decoder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mftrcrd) (version `16.35c3ac2f`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S mftrcrd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/MftRcrd.git
cd MftRcrd
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

Backlinks: [[forensic]] · [[mftrcrd]] · [[BlackArch Tools]]

