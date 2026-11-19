---
tool: "mingsweeper"
build-system: "windows"
upstream: "http://www.hoobie.net/mingsweeper/"
recipe: "blackarch"
source: "http://www.hoobie.net/mingsweeper/minger1a5.zip"
license: "custom:unknown"
blackarch-pkgver: "1.00"
---

# Installing mingsweeper

> A network reconnaissance tool designed to facilitate large address space,high speed node discovery and identification.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mingsweeper) (version `1.00`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mingsweeper    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hoobie.net/mingsweeper/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[mingsweeper]] · [[BlackArch Tools]]

