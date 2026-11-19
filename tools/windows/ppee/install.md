---
tool: "ppee"
build-system: "windows"
upstream: "https://www.mzrst.com/"
recipe: "blackarch"
source: "https://www.mzrst.com/"
license: "custom:unknown"
blackarch-pkgver: "1.12"
---

# Installing ppee

> A Professional PE file Explorer for reversers, malware researchers and those who want to statically inspect PE files in more details.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ppee) (version `1.12`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ppee    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.mzrst.com/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[ppee]] · [[BlackArch Tools]]

