---
tool: "powershdll"
build-system: "windows"
upstream: "https://github.com/p3nt4/PowerShdll"
recipe: "blackarch"
source: "https://github.com/p3nt4/PowerShdll"
license: "MIT"
blackarch-pkgver: "72.62cfa17"
---

# Installing powershdll

> Run PowerShell with rundll32. Bypass software restrictions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powershdll) (version `72.62cfa17`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S powershdll    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/p3nt4/PowerShdll
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[powershdll]] · [[BlackArch Tools]]

