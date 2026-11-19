---
tool: "ldapmonitor"
build-system: "windows"
upstream: "https://github.com/p0dalirius/ldapmonitor"
recipe: "blackarch"
nix-package: "ldapmonitor"
source: "https://github.com/p0dalirius/ldapmonitor"
license: "GPL-3.0-only"
blackarch-pkgver: "1.4.r13.g6f9dfcb"
---

# Installing ldapmonitor

> Monitor creation, deletion and changes to LDAP objects live during your pentest or system administration!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ldapmonitor) (version `1.4.r13.g6f9dfcb`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S ldapmonitor    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/p0dalirius/ldapmonitor
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `pycryptodome`, `sectools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[ldapmonitor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ldapmonitor    # ad-hoc run, no install
nix profile install nixpkgs#ldapmonitor   # or persist it
```
