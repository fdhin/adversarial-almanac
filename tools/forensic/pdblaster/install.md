---
tool: "pdblaster"
build-system: "scripts"
upstream: "https://github.com/SecurityRiskAdvisors/PDBlaster"
recipe: "blackarch"
source: "https://github.com/SecurityRiskAdvisors/PDBlaster"
license: "GPL3"
blackarch-pkgver: "4.fc8abb3"
---

# Installing pdblaster

> Extract PDB file paths from large sample sets of executable files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdblaster) (version `4.fc8abb3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pdblaster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SecurityRiskAdvisors/PDBlaster.git
cd PDBlaster
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pandas`, `pefile`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pdblaster]] · [[BlackArch Tools]]

