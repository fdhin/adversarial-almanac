---
tool: "pyadrecon"
build-system: "windows"
upstream: "https://github.com/l4rm4nd/PyADRecon"
recipe: "blackarch"
source: "https://github.com/l4rm4nd/PyADRecon"
license: "MIT"
blackarch-pkgver: "0.10.2"
---

# Installing pyadrecon

> Gathers information about the Active Directory and generates a report which can provide a holistic picture of the current state of the target AD environment.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyadrecon) (version `0.10.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pyadrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/l4rm4nd/PyADRecon
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `gssapi`, `ldap3`, `openpyxl`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[pyadrecon]] · [[BlackArch Tools]]

