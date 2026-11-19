---
tool: "hekatomb"
build-system: "windows"
upstream: "https://github.com/ProcessusT/HEKATOMB"
recipe: "blackarch"
nix-package: "hekatomb"
source: "https://github.com/ProcessusT/HEKATOMB"
license: "GPL3"
blackarch-pkgver: "107.bdd53cf"
---

# Installing hekatomb

> Extract and decrypt all credentials from all domain computers using DPAPI.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hekatomb) (version `107.bdd53cf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hekatomb    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/ProcessusT/HEKATOMB
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `chardet`, `dnspython`, `ldap3`, `pip`, `pycryptodomex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[hekatomb]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#hekatomb    # ad-hoc run, no install
nix profile install nixpkgs#hekatomb   # or persist it
```
