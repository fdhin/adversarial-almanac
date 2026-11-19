---
tool: "pe-bear"
build-system: "windows"
upstream: "https://github.com/hasherezade/pe-bear"
recipe: "blackarch"
nix-package: "pe-bear"
aur-package: "pe-bear"
source: "https://github.com/hasherezade/pe-bear"
license: "custom:unknown"
blackarch-pkgver: "0.7.2"
---

# Installing pe-bear

> A freeware reversing tool for PE files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pe-bear) (version `0.7.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pe-bear    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/hasherezade/pe-bear
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pe-bear]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pe-bear    # ad-hoc run, no install
nix profile install nixpkgs#pe-bear   # or persist it
```
