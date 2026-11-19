---
tool: "evil-winrm-py"
build-system: "windows"
upstream: "https://github.com/adityatelange/evil-winrm-py"
recipe: "blackarch"
nix-package: "evil-winrm-py"
aur-package: "evil-winrm-py"
source: "https://github.com/adityatelange/evil-winrm-py"
license: "MIT"
blackarch-pkgver: "1.7.0"
---

# Installing evil-winrm-py

> WinRM shell for Windows and Active Directory pentesting.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evil-winrm-py) (version `1.7.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S evil-winrm-py    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/adityatelange/evil-winrm-py
wine <tool>.exe
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `prompt_toolkit`, `pypsrp`, `setuptools`, `tqdm`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[evil-winrm-py]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#evil-winrm-py    # ad-hoc run, no install
nix profile install nixpkgs#evil-winrm-py   # or persist it
```
