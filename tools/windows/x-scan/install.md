---
tool: "x-scan"
build-system: "windows"
upstream: "http://www.xfocus.org/"
recipe: "blackarch"
source: "http://www.xfocus.org/"
license: "custom:unknown"
blackarch-pkgver: "3.3"
---

# Installing x-scan

> A general network vulnerabilities scanner for scanning network vulnerabilities for specific IP address scope or stand-alone computer by multi-threading method, plug-ins are supportable.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/x-scan) (version `3.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S x-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.xfocus.org/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[x-scan]] · [[BlackArch Tools]]

