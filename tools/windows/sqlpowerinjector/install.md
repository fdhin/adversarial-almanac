---
tool: "sqlpowerinjector"
build-system: "windows"
upstream: "http://www.sqlpowerinjector.com/download.htm"
recipe: "blackarch"
source: "https://downloads.sourceforge.net/project/spinj/spinj/SQL%20Power%20Injector%20v%201.2/PackageSPinjv1-2.rar"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing sqlpowerinjector

> Application created in .Net 1.1 that helps the penetration tester to find and exploit SQL injections on a web page.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqlpowerinjector) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sqlpowerinjector    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.sqlpowerinjector.com/download.htm
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sqlpowerinjector]] · [[BlackArch Tools]]

