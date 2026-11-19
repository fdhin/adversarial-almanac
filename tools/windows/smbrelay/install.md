---
tool: "smbrelay"
build-system: "windows"
upstream: "http://www.tarasco.org/security/smbrelay/"
recipe: "blackarch"
source: "http://www.tarasco.org/security/smbrelay/"
license: "GPL"
blackarch-pkgver: "3"
---

# Installing smbrelay

> SMB / HTTP to SMB replay attack toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbrelay) (version `3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S smbrelay    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.tarasco.org/security/smbrelay/
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `wine`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[smbrelay]] · [[BlackArch Tools]]

