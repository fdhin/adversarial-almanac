---
tool: "pwdump"
build-system: "windows"
upstream: "http://www.tarasco.org/security/pwdump_7/index.html"
recipe: "blackarch"
source: "http://www.tarasco.org/security/pwdump_7/pwdump7.zip"
license: "custom"
blackarch-pkgver: "7.1"
---

# Installing pwdump

> Extracts the binary SAM and SYSTEM file from the filesystem and then the hashes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwdump) (version `7.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S pwdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.tarasco.org/security/pwdump_7/index.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pwdump]] · [[BlackArch Tools]]

