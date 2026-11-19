---
tool: "sqlping"
build-system: "windows"
upstream: "http://www.sqlsecurity.com/downloads"
recipe: "blackarch"
source: "http://www.sqlsecurity.com/downloads"
license: "custom"
blackarch-pkgver: "4"
---

# Installing sqlping

> SQL Server scanning tool that also checks for weak passwords using wordlists.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqlping) (version `4`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S sqlping    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.sqlsecurity.com/downloads
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sqlping]] · [[BlackArch Tools]]

