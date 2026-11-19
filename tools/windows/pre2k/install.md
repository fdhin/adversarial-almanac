---
tool: "pre2k"
build-system: "windows"
upstream: "https://github.com/garrettfoster13/pre2k-TS"
recipe: "blackarch"
nix-package: "pre2k"
source: "https://github.com/garrettfoster13/pre2k-TS"
license: "MIT"
blackarch-pkgver: "24.ff51b3b"
---

# Installing pre2k

> Query for existence of pre-windows 2000 computer objects which can be leveraged to gain a foothold in a target domain.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pre2k) (version `24.ff51b3b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pre2k    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/garrettfoster13/pre2k-TS
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `pyasn1`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[pre2k]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pre2k    # ad-hoc run, no install
nix profile install nixpkgs#pre2k   # or persist it
```
