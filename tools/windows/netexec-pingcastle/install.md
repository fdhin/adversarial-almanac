---
tool: "netexec-pingcastle"
build-system: "windows"
upstream: "https://github.com/TRIKKSS/CrackMapExec-PingCastle"
recipe: "blackarch"
source: "https://github.com/TRIKKSS/CrackMapExec-PingCastle"
license: "custom:unknown"
blackarch-pkgver: "9.16340d2"
---

# Installing netexec-pingcastle

> NetExec & CrackMapExec module that execute PingCastle on a remote machine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netexec-pingcastle) (version `9.16340d2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S netexec-pingcastle    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/TRIKKSS/CrackMapExec-PingCastle
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/crackmapexec`, `/usr/share/netexec`

Backlinks: [[windows]] · [[netexec-pingcastle]] · [[BlackArch Tools]]

