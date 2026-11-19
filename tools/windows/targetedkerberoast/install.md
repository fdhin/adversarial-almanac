---
tool: "targetedkerberoast"
build-system: "windows"
upstream: "https://github.com/ShutdownRepo/targetedkerberoast"
recipe: "blackarch"
source: "https://github.com/ShutdownRepo/targetedkerberoast"
license: "GPL-3.0-or-later"
blackarch-pkgver: "27.ebed079"
---

# Installing targetedkerberoast

> Kerberoast with ACL abuse capabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/targetedkerberoast) (version `27.ebed079`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S targetedkerberoast    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/ShutdownRepo/targetedkerberoast
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `pyasn1`, `pycryptodome`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[targetedkerberoast]] · [[BlackArch Tools]]

