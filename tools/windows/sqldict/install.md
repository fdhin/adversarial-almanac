---
tool: "sqldict"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/sqldict/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/sqldict/"
license: "custom:unknown"
blackarch-pkgver: "2.1"
kali-package: "sqldict"
kali-version: "2.1-1kali6"
---

# Installing sqldict

> A dictionary attack tool for SQL Server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqldict) (version `2.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sqldict    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sqldict    # Kali package version 2.1-1kali6
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/sqldict/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sqldict]] · [[BlackArch Tools]]

