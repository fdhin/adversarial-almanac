---
tool: "mimikatz"
build-system: "windows"
upstream: "https://github.com/gentilkiwi/mimikatz"
recipe: "blackarch"
nix-package: "mimikatz"
source: "https://github.com/gentilkiwi/mimikatz"
license: "custom:unknown"
blackarch-pkgver: "2.2.0_20220919"
kali-package: "mimikatz"
kali-version: "2.2.0-git20220919-0kali1"
---

# Installing mimikatz

> A little tool to play with Windows security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mimikatz) (version `2.2.0_20220919`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mimikatz    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install mimikatz    # Kali package version 2.2.0-git20220919-0kali1
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/gentilkiwi/mimikatz
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[mimikatz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mimikatz    # ad-hoc run, no install
nix profile install nixpkgs#mimikatz   # or persist it
```
