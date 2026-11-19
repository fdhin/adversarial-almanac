---
tool: "gsd"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/gsd/"
recipe: "blackarch"
nix-package: "gsd"
source: "https://vidstromlabs.com/freetools/gsd/"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing gsd

> Gives you the Discretionary Access Control List of any Windows NT service you specify as a command line option.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gsd) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gsd    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/gsd/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[gsd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gsd    # ad-hoc run, no install
nix profile install nixpkgs#gsd   # or persist it
```
