---
tool: "invoke-cradlecrafter"
build-system: "scripts"
upstream: "https://github.com/danielbohannon/Invoke-CradleCrafter"
recipe: "blackarch"
source: "https://github.com/danielbohannon/Invoke-CradleCrafter"
license: "Apache"
blackarch-pkgver: "19.3ff8bac"
---

# Installing invoke-cradlecrafter

> PowerShell Remote Download Cradle Generator & Obfuscator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/invoke-cradlecrafter) (version `19.3ff8bac`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S invoke-cradlecrafter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/danielbohannon/Invoke-CradleCrafter
cd Invoke-CradleCrafter
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[automation]] · [[invoke-cradlecrafter]] · [[BlackArch Tools]]

