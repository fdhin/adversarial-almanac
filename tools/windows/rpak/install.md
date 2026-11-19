---
tool: "rpak"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/promisdetect/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/promisdetect/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing rpak

> A collection of tools that can be useful for doing attacks on routing protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rpak) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rpak    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/promisdetect/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[rpak]] · [[BlackArch Tools]]

