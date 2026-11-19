---
tool: "brute12"
build-system: "windows"
upstream: "http://www.security-projects.com/?Brute12"
recipe: "blackarch"
source: "http://www.security-projects.com/?Brute12"
license: "custom:unknown"
blackarch-pkgver: "1"
---

# Installing brute12

> A tool designed for auditing the cryptography container security in PKCS12 format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/brute12) (version `1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S brute12    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.security-projects.com/?Brute12
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[brute12]] · [[BlackArch Tools]]

