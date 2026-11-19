---
tool: "conpass"
build-system: "windows"
upstream: "https://github.com/login-securite/conpass"
recipe: "blackarch"
nix-package: "conpass"
source: "https://github.com/login-securite/conpass"
license: "MIT"
blackarch-pkgver: "0.1.1"
---

# Installing conpass

> Password spraying in AD environment avoing account locking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/conpass) (version `0.1.1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S conpass    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/login-securite/conpass
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap`, `rich`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[conpass]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#conpass    # ad-hoc run, no install
nix profile install nixpkgs#conpass   # or persist it
```
