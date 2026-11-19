---
tool: "pygpoabuse"
build-system: "windows"
upstream: "https://github.com/Hackndo/pyGPOAbuse"
recipe: "blackarch"
nix-package: "pygpoabuse"
source: "https://github.com/Hackndo/pyGPOAbuse"
license: "MIT"
blackarch-pkgver: "51.c18e1de"
---

# Installing pygpoabuse

> RCE via GPO scheduled tasks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pygpoabuse) (version `51.c18e1de`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pygpoabuse    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/Hackndo/pyGPOAbuse
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `msldap`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[pygpoabuse]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pygpoabuse    # ad-hoc run, no install
nix profile install nixpkgs#pygpoabuse   # or persist it
```
