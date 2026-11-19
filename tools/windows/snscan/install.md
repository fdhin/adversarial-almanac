---
tool: "snscan"
build-system: "windows"
upstream: "http://www.mcafee.com/uk/downloads/free-tools/snscan.aspx"
recipe: "blackarch"
source: "http://www.mcafee.com/uk/downloads/free-tools/snscan.aspx"
license: "custom:unknown"
blackarch-pkgver: "1.05"
---

# Installing snscan

> A Windows based SNMP detection utility that can quickly and accurately identify SNMP enabled devices on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snscan) (version `1.05`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S snscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.mcafee.com/uk/downloads/free-tools/snscan.aspx
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[snscan]] · [[BlackArch Tools]]

