---
tool: "brutus"
build-system: "windows"
upstream: "http://www.hoobie.net/brutus/"
recipe: "blackarch"
nix-package: "brutus"
source: "http://www.hoobie.net/brutus/brutus-aet2.zip"
license: "custom"
blackarch-pkgver: "2"
---

# Installing brutus

> One of the fastest, most flexible remote password crackers you can get your hands on.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brutus) (version `2`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S brutus    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hoobie.net/brutus/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[brutus]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#brutus    # ad-hoc run, no install
nix profile install nixpkgs#brutus   # or persist it
```
