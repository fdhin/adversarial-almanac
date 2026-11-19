---
tool: "petools"
build-system: "windows"
upstream: "https://github.com/petoolse/petools"
recipe: "blackarch"
source: "https://github.com/petoolse/petools"
license: "MIT"
blackarch-pkgver: "1.9.762"
---

# Installing petools

> Portable executable (PE) manipulation toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/petools) (version `1.9.762`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S petools    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/petoolse/petools
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[petools]] · [[BlackArch Tools]]

