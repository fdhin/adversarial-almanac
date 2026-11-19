---
tool: "pmdump"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/pmdump/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/pmdump/"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing pmdump

> A tool that lets you dump the memory contents of a process to a file without stopping the process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pmdump) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pmdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/pmdump/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pmdump]] · [[BlackArch Tools]]

