---
tool: "chameleon"
build-system: "scripts"
upstream: "https://github.com/mdsecactivebreach/Chameleon"
recipe: "blackarch"
aur-package: "chameleon"
source: "https://github.com/mdsecactivebreach/Chameleon"
license: "custom:unknown"
blackarch-pkgver: "29.4414287"
---

# Installing chameleon

> A tool for evading Proxy categorisation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chameleon) (version `29.4414287`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chameleon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mdsecactivebreach/Chameleon.git
cd Chameleon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `dateutil`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[chameleon]] · [[BlackArch Tools]]

