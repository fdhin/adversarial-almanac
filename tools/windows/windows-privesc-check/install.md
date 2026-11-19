---
tool: "windows-privesc-check"
build-system: "windows"
upstream: "https://github.com/pentestmonkey/windows-privesc-check"
recipe: "blackarch"
source: "https://github.com/pentestmonkey/windows-privesc-check"
license: "custom:unknown"
blackarch-pkgver: "181.9f304fd"
kali-package: "windows-privesc-check"
kali-version: "2.0.0+svn197-0kali5"
---

# Installing windows-privesc-check

> Standalone Executable to Check for Simple Privilege Escalation Vectors on Windows Systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windows-privesc-check) (version `181.9f304fd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S windows-privesc-check    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install windows-privesc-check    # Kali package version 2.0.0+svn197-0kali5
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/pentestmonkey/windows-privesc-check
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[windows-privesc-check]] · [[BlackArch Tools]]

