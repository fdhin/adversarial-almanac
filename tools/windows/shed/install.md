---
tool: "shed"
build-system: "windows"
upstream: "https://github.com/enkomio/shed/releases"
recipe: "blackarch"
aur-package: "shed"
source: "https://github.com/enkomio/shed/releases"
license: "custom:unknown"
blackarch-pkgver: "2.0.0"
---

# Installing shed

> .NET runtime inspector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shed) (version `2.0.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shed    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/enkomio/shed
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `lib32-gnutls`
  - `wine-mono`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[shed]] · [[BlackArch Tools]]

