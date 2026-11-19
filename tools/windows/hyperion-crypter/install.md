---
tool: "hyperion-crypter"
build-system: "windows"
upstream: "http://nullsecurity.net/tools/binary.html"
recipe: "blackarch"
source: "http://nullsecurity.net/tools/binary.html"
license: "custom:unknown"
blackarch-pkgver: "2.3.1"
---

# Installing hyperion-crypter

> A runtime encrypter for 32-bit and 64-bit portable executables.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hyperion-crypter) (version `2.3.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hyperion-crypter    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://nullsecurity.net/tools/binary.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[hyperion-crypter]] · [[BlackArch Tools]]

