---
tool: "pe-sieve"
build-system: "windows"
upstream: "https://github.com/hasherezade/pe-sieve"
recipe: "blackarch"
source: "https://github.com/hasherezade/pe-sieve"
license: "BSD"
blackarch-pkgver: "0.4.1.1"
---

# Installing pe-sieve

> Scans a given process. Recognizes and dumps a variety of potentially malicious implants (replaced/injected PEs, shellcodes, hooks, in-memory patches).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pe-sieve) (version `0.4.1.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pe-sieve    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/hasherezade/pe-sieve
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pe-sieve]] · [[BlackArch Tools]]

