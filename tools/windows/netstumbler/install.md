---
tool: "netstumbler"
build-system: "windows"
upstream: "http://www.netstumbler.com/downloads/"
recipe: "blackarch"
source: "http://www.netstumbler.com/downloads/"
license: "custom:unknown"
blackarch-pkgver: "0.4.0"
---

# Installing netstumbler

> Well-known wireless AP scanner and sniffer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netstumbler) (version `0.4.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S netstumbler    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.netstumbler.com/downloads/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[netstumbler]] · [[BlackArch Tools]]

