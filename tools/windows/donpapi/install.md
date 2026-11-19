---
tool: "donpapi"
build-system: "windows"
upstream: "https://github.com/login-securite/DonPAPI"
recipe: "blackarch"
nix-package: "donpapi"
source: "https://github.com/login-securite/DonPAPI.git"
license: "custom:unknown"
blackarch-pkgver: "V1.2.0.r41.g4f01e2b"
---

# Installing donpapi

> Dumping revelant information on compromised targets without AV detection with DPAPI.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/donpapi) (version `V1.2.0.r41.g4f01e2b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S donpapi    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/login-securite/DonPAPI
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `dploot`, `flask`, `flask-basicauth`, `flask-cors`, `flask-rich`, `jinja`, `marshmallow-sqlalchemy`, `pip`, `pyjwt`, `rich`, `sqlalchemy`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `exrex`
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[donpapi]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#donpapi    # ad-hoc run, no install
nix profile install nixpkgs#donpapi   # or persist it
```
