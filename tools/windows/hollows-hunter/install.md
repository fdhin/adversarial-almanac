---
tool: "hollows-hunter"
build-system: "windows"
upstream: "https://github.com/hasherezade/hollows_hunter"
recipe: "blackarch"
source: "https://github.com/hasherezade/hollows_hunter"
license: "BSD"
blackarch-pkgver: "0.4.1.1"
---

# Installing hollows-hunter

> Scans all running processes. Recognizes and dumps a variety of potentially malicious implants (replaced/injected PEs, shellcodes, hooks, in-memory patches).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hollows-hunter) (version `0.4.1.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S hollows-hunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/hasherezade/hollows_hunter
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[hollows-hunter]] · [[BlackArch Tools]]

