---
tool: "certipy"
build-system: "windows"
upstream: "https://github.com/ly4k/Certipy"
recipe: "blackarch"
nix-package: "certipy"
source: "https://github.com/ly4k/Certipy"
license: "MIT"
blackarch-pkgver: "5.1.0.r12.g3ae3442"
---

# Installing certipy

> Active Directory Certificate Services enumeration and abuse.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/certipy) (version `5.1.0.r12.g3ae3442`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S certipy    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/ly4k/Certipy
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[certipy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#certipy    # ad-hoc run, no install
nix profile install nixpkgs#certipy   # or persist it
```
