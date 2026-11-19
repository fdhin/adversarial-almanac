---
tool: "gitgraber"
build-system: "scripts"
upstream: "https://github.com/hisxo/gitGraber"
recipe: "blackarch"
source: "https://github.com/hisxo/gitGraber"
license: "GPL3"
blackarch-pkgver: "82.aab4839"
---

# Installing gitgraber

> Monitor GitHub to search and find sensitive data in real time for different online services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitgraber) (version `82.aab4839`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gitgraber    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/hisxo/gitGraber.git
cd gitGraber
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `argcomplete`, `python-crontab`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitgraber]] · [[BlackArch Tools]]

