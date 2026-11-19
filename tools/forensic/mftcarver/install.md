---
tool: "mftcarver"
build-system: "scripts"
upstream: "https://github.com/jschicht/MftCarver"
recipe: "blackarch"
source: "https://github.com/jschicht/MftCarver"
license: "CCPL"
blackarch-pkgver: "9.7bfcc0a2"
---

# Installing mftcarver

> Carve $MFT records from a chunk of data (for instance a memory dump).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mftcarver) (version `9.7bfcc0a2`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S mftcarver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/MftCarver.git
cd MftCarver
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

Backlinks: [[forensic]] · [[mftcarver]] · [[BlackArch Tools]]

