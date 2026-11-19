---
tool: "uacme"
build-system: "windows"
upstream: "https://github.com/hfiref0x/UACME"
recipe: "blackarch"
nix-package: "uacme"
aur-package: "uacme"
source: "https://github.com/hfiref0x/UACME"
license: "unknown"
blackarch-pkgver: "319.3f1885c"
---

# Installing uacme

> Defeating Windows User Account Control.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uacme) (version `319.3f1885c`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S uacme    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/hfiref0x/UACME
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[uacme]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#uacme    # ad-hoc run, no install
nix profile install nixpkgs#uacme   # or persist it
```
