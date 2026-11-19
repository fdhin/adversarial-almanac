---
tool: "gpowned"
build-system: "windows"
upstream: "https://github.com/X-C3LL/gpowned"
recipe: "blackarch"
source: "https://github.com/X-C3LL/gpowned"
license: "custom:custom"
blackarch-pkgver: "25.5de0c77"
---

# Installing gpowned

> GPOs manipulation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gpowned) (version `25.5de0c77`, license custom:custom).

## Arch / BlackArch

```bash
sudo pacman -S gpowned    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/X-C3LL/gpowned
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[gpowned]] · [[BlackArch Tools]]

