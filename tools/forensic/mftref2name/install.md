---
tool: "mftref2name"
build-system: "scripts"
upstream: "https://github.com/jschicht/MftRef2Name"
recipe: "blackarch"
source: "https://github.com/jschicht/MftRef2Name"
license: "CCPL"
blackarch-pkgver: "6.7df9eebb"
---

# Installing mftref2name

> Resolve file index number to name or vice versa on NTFS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mftref2name) (version `6.7df9eebb`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S mftref2name    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jschicht/MftRef2Name.git
cd MftRef2Name
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

Backlinks: [[forensic]] · [[mftref2name]] · [[BlackArch Tools]]

