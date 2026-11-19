---
tool: "unsecure"
build-system: "windows"
upstream: "http://www.sniperx.net/"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Crackers/NT/uns12exe.zip"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing unsecure

> Bruteforces network login masks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unsecure) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S unsecure    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.sniperx.net/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[unsecure]] · [[BlackArch Tools]]

