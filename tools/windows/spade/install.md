---
tool: "spade"
build-system: "windows"
upstream: "http://www.hoobie.net/brutus/"
recipe: "blackarch"
nix-package: "spade"
source: "http://www.hoobie.net/brutus/"
license: "custom:unknown"
blackarch-pkgver: "114"
---

# Installing spade

> A general-purpose Internet utility package, with some extra features to help in tracing the source of spam and other forms of Internet harassment.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spade) (version `114`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spade    # needs the [blackarch] repo enabled
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

Backlinks: [[windows]] · [[spade]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#spade    # ad-hoc run, no install
nix profile install nixpkgs#spade   # or persist it
```
