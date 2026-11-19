---
tool: "lethalhta"
build-system: "windows"
upstream: "https://github.com/codewhitesec/LethalHTA"
recipe: "blackarch"
source: "https://github.com/codewhitesec/LethalHTA"
license: "MIT"
blackarch-pkgver: "2.5602402"
---

# Installing lethalhta

> Lateral Movement technique using DCOM and HTA.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lethalhta) (version `2.5602402`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S lethalhta    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/codewhitesec/LethalHTA
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`, `mono`
- **System libraries** (Arch name — translate for your distro):
  - `mono-msbuild`
  - `mono-msbuild-sdkresolver`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[lethalhta]] · [[BlackArch Tools]]

