---
tool: "firebaseenum"
build-system: "scripts"
upstream: "https://github.com/Sambal0x/firebaseEnum"
recipe: "blackarch"
source: "https://github.com/Sambal0x/firebaseEnum"
license: "custom:unknown"
blackarch-pkgver: "19.1cbd19c"
---

# Installing firebaseenum

> Tool to mass analyse potentially exposed Firebase databases on Android apps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firebaseenum) (version `19.1cbd19c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S firebaseenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Sambal0x/firebaseEnum.git
cd firebaseEnum
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bs4`, `requests-futures`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[firebaseenum]] · [[BlackArch Tools]]

