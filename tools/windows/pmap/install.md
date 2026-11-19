---
tool: "pmap"
build-system: "windows"
upstream: "http://www.hellfiresecurity.com/tools.htm"
recipe: "blackarch"
source: "http://www.hellfiresecurity.com/tools.htm"
license: "custom:unknown"
blackarch-pkgver: "1.10"
---

# Installing pmap

> Passively discover, scan, and fingerprint link-local peers by the background noise they generate (i.e. their broadcast and multicast traffic).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pmap) (version `1.10`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hellfiresecurity.com/tools.htm
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pmap]] · [[BlackArch Tools]]

