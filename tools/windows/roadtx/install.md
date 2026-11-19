---
tool: "roadtx"
build-system: "windows"
upstream: "https://github.com/dirkjanm/ROADtools"
recipe: "split-parent:roadtools"
source: "https://github.com/dirkjanm/ROADtools"
license: "MIT"
blackarch-pkgver: "511.f7b0c47"
provides: ["genconfig"]
---

# Installing roadtx

> Azure AD and O365 exploration framework.

ℹ️ Packaged as a split package — built from the `roadtools` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/roadtools) (version `511.f7b0c47`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S roadtx    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/dirkjanm/ROADtools
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`
- **Build tools**: `git`, `npm`

## Installed layout (per recipe)

- Commands: `/usr/bin/genconfig`

Backlinks: [[windows]] · [[roadtx]] · [[BlackArch Tools]]

