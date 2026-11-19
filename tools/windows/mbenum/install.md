---
tool: "mbenum"
build-system: "windows"
upstream: "http://www.cqure.net/wp/mbenum/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/mbenum/"
license: "GPL"
blackarch-pkgver: "1.5.0"
---

# Installing mbenum

> Queries the master browser for whatever information it has registered.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mbenum) (version `1.5.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S mbenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.cqure.net/wp/mbenum/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[mbenum]] · [[BlackArch Tools]]

