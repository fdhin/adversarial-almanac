---
tool: "hexodus"
build-system: "windows"
upstream: "https://github.com/0xdsm/hexodus"
recipe: "blackarch"
source: "https://github.com/0xdsm/hexodus"
license: "MIT"
blackarch-pkgver: "72.69a96f6"
---

# Installing hexodus

> Python framework project designed to enumerate and help in Active Directory attacks through Windows protocols like SMB, LDAP, WinRM and other.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hexodus) (version `72.69a96f6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S hexodus    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/0xdsm/hexodus
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `flask`, `installer`, `ldap3`, `rich`, `setuptools`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[hexodus]] · [[BlackArch Tools]]

