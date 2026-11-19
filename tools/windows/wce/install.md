---
tool: "wce"
build-system: "windows"
upstream: "http://www.hoobie.net/wce/"
recipe: "blackarch"
source: "http://www.ampliasecurity.com/research/wce_v1_41beta_universal.zip"
license: "custom:unknown"
blackarch-pkgver: "1.41beta"
---

# Installing wce

> A security tool to list logon sessions and add, change, list and delete associated credentials (ex.: LM/NT hashes, plaintext passwords and Kerberos tickets).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wce) (version `1.41beta`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wce    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hoobie.net/wce/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[wce]] · [[BlackArch Tools]]

