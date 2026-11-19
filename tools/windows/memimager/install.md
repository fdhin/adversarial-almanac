---
tool: "memimager"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/memimager/"
recipe: "blackarch"
source: "http://ntsecurity.nu/downloads/memimager.exe"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing memimager

> Performs a memory dump using NtSystemDebugControl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/memimager) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S memimager    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/memimager/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[memimager]] · [[BlackArch Tools]]

