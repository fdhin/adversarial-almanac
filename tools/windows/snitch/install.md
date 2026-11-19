---
tool: "snitch"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/snitch/"
recipe: "blackarch"
nix-package: "snitch"
aur-package: "snitch"
source: "http://ntsecurity.nu/toolbox/snitch/"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing snitch

> Turn back the asterisks in password fields to plaintext passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snitch) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S snitch    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/snitch/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[snitch]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snitch    # ad-hoc run, no install
nix profile install nixpkgs#snitch   # or persist it
```
