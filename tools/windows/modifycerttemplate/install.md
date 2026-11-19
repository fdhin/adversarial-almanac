---
tool: "modifycerttemplate"
build-system: "windows"
upstream: "https://github.com/fortalice/modifyCertTemplate"
recipe: "blackarch"
source: "https://github.com/fortalice/modifyCertTemplate"
license: "custom:unknown"
blackarch-pkgver: "7.4c35708"
---

# Installing modifycerttemplate

> Aid operators in modifying ADCS certificate templates so that a created vulnerable state can be leveraged for privilege escalation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/modifycerttemplate) (version `7.4c35708`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S modifycerttemplate    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/fortalice/modifyCertTemplate
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

Backlinks: [[windows]] · [[modifycerttemplate]] · [[BlackArch Tools]]

