---
tool: "indxcarver"
build-system: "scripts"
upstream: "https://github.com/jschicht/IndxCarver"
recipe: "blackarch"
source: "https://github.com/jschicht/IndxCarver"
license: "CCPL"
blackarch-pkgver: "5.dee36608"
---

# Installing indxcarver

> Carve INDX records from a chunk of data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/indxcarver) (version `5.dee36608`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S indxcarver    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/IndxCarver.git
cd IndxCarver
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

Backlinks: [[forensic]] · [[indxcarver]] · [[BlackArch Tools]]

