---
tool: "dumpusers"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/dumpusers/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/dumpusers/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing dumpusers

> Dumps account names and information even though RestrictAnonymous has been set to 1.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dumpusers) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dumpusers    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/dumpusers/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[dumpusers]] · [[BlackArch Tools]]

