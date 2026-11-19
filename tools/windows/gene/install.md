---
tool: "gene"
build-system: "windows"
upstream: "https://github.com/0xrawsec/gene"
recipe: "blackarch"
source: "https://github.com/0xrawsec/gene"
license: "GPL3"
blackarch-pkgver: "78.faf8cc0"
---

# Installing gene

> Signature Engine for Windows Event Logs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gene) (version `78.faf8cc0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gene    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/0xrawsec/gene
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`, `go`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[gene]] · [[BlackArch Tools]]

