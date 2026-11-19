---
tool: "adpeas"
build-system: "windows"
upstream: "https://github.com/ajm4n/adpeas"
recipe: "blackarch"
source: "https://github.com/ajm4n/adpeas"
license: "MIT"
blackarch-pkgver: "1.3.0.r15.g0bfcedc"
provides: ["adPEAS"]
---

# Installing adpeas

> winPEAS, but for Active Directory.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adpeas) (version `1.3.0.r15.g0bfcedc`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S adpeas    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/ajm4n/adpeas
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `regex`, `setuptools`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bloodhound`
  - `certi`
  - `certipy`
  - `impacket`
  - `netexec`
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/adPEAS`

Backlinks: [[windows]] · [[adpeas]] · [[BlackArch Tools]]

