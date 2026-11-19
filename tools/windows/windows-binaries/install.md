---
tool: "windows-binaries"
build-system: "windows"
upstream: "https://github.com/BlackArch/windows-binaries"
recipe: "blackarch"
aur-package: "windows-binaries"
source: "https://github.com/BlackArch/windows-binaries"
license: "custom:unknown"
blackarch-pkgver: "20.7d272da"
---

# Installing windows-binaries

> A colleciton of pentesting Windows binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windows-binaries) (version `20.7d272da`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S windows-binaries    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/BlackArch/windows-binaries
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[windows-binaries]] · [[BlackArch Tools]]

