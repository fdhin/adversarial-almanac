---
tool: "browselist"
build-system: "windows"
upstream: "https://vidstromlabs.com/browselist/"
recipe: "blackarch"
source: "https://vidstromlabs.com/browselist/"
license: "custom:unknown"
blackarch-pkgver: "1.4"
---

# Installing browselist

> Retrieves the browse list ; the output list contains computer names, and the roles they play in the network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/browselist) (version `1.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S browselist    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/browselist/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[browselist]] · [[BlackArch Tools]]

