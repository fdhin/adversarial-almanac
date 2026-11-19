---
tool: "certi"
build-system: "windows"
upstream: "https://github.com/zer1t0/certi"
recipe: "blackarch"
nix-package: "certi"
source: "https://github.com/zer1t0/certi"
license: "AGPL-3.0-only"
blackarch-pkgver: "6.6cfa656"
---

# Installing certi

> Active Directory Certificate Services (ADCS) abuser. impacket copy of Certify.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/certi) (version `6.6cfa656`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S certi    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/zer1t0/certi
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `cryptography`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[certi]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#certi    # ad-hoc run, no install
nix profile install nixpkgs#certi   # or persist it
```
