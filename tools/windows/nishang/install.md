---
tool: "nishang"
build-system: "windows"
upstream: "https://github.com/samratashok/nishang/"
recipe: "blackarch"
source: "https://github.com/samratashok/nishang/"
license: "GPL3"
blackarch-pkgver: "0.7.6"
kali-package: "nishang"
kali-version: "0.7.6+git20210724.414ee11-0kali1"
---

# Installing nishang

> Using PowerShell for Penetration Testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nishang) (version `0.7.6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nishang    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install nishang    # Kali package version 0.7.6+git20210724.414ee11-0kali1
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://code.google.com/p/nishang/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[nishang]] · [[BlackArch Tools]]

