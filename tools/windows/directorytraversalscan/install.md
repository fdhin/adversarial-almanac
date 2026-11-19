---
tool: "directorytraversalscan"
build-system: "windows"
upstream: "http://sourceforge.net/projects/httpdirscan/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/httpdirscan/"
license: "custom:unknown"
blackarch-pkgver: "1.0.1.0"
---

# Installing directorytraversalscan

> Detect directory traversal vulnerabilities in HTTP servers and web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/directorytraversalscan) (version `1.0.1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S directorytraversalscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://sourceforge.net/projects/httpdirscan/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[directorytraversalscan]] · [[BlackArch Tools]]

