---
tool: "orakelcrackert"
build-system: "windows"
upstream: "https://github.com/vanhauser-thc/THC-Archive/blob/master/Tools/thc-orakelcrackert11g.tar.gz"
recipe: "blackarch"
source: "https://github.com/vanhauser-thc/THC-Archive/raw/master/Tools/thc-orakelcrackert11g.tar.gz"
license: "custom"
blackarch-pkgver: "1.00"
---

# Installing orakelcrackert

> This tool can crack passwords which are encrypted using Oracle's latest SHA1 based password protection algorithm.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/orakelcrackert) (version `1.00`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S orakelcrackert    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/vanhauser-thc/THC-Archive/blob/master/Tools/thc-orakelcrackert11g.tar.gz
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[orakelcrackert]] · [[BlackArch Tools]]

