---
tool: "rpcsniffer"
build-system: "windows"
upstream: "https://github.com/AdiKo/RPCSniffer"
recipe: "blackarch"
source: "https://github.com/AdiKo/RPCSniffer"
license: "custom:unknown"
blackarch-pkgver: "7.9fab095"
---

# Installing rpcsniffer

> Sniffs WINDOWS RPC messages in a given RPC server process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rpcsniffer) (version `7.9fab095`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rpcsniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/AdiKo/RPCSniffer
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[rpcsniffer]] · [[BlackArch Tools]]

