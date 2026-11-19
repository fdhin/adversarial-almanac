---
tool: "winrelay"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/winrelay/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/winrelay/"
license: "custom:unknown"
blackarch-pkgver: "2.0"
---

# Installing winrelay

> A TCP/UDP forwarder/redirector that works with both IPv4 and IPv6.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/winrelay) (version `2.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S winrelay    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/winrelay/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[winrelay]] · [[BlackArch Tools]]

