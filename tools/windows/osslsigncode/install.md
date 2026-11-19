---
tool: "osslsigncode"
build-system: "windows"
upstream: "https://github.com/mtrojnar/osslsigncode"
recipe: "blackarch"
nix-package: "osslsigncode"
aur-package: "osslsigncode"
source: "https://github.com/mtrojnar/osslsigncode"
license: "GPL3"
blackarch-pkgver: "750.868d6e9"
---

# Installing osslsigncode

> A small tool that implements part of the functionality of the Microsoft tool signtool.exe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osslsigncode) (version `750.868d6e9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S osslsigncode    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/mtrojnar/osslsigncode
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[osslsigncode]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#osslsigncode    # ad-hoc run, no install
nix profile install nixpkgs#osslsigncode   # or persist it
```
