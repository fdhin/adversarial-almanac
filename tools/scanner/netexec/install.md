---
tool: "netexec"
build-system: "python"
upstream: "https://netexec.wiki/"
recipe: "blackarch"
nix-package: "netexec"
aur-package: "netexec"
source: "https://netexec.wiki/"
license: "BSD"
blackarch-pkgver: "v1.5.1.r1170.g93d62e5"
provides: ["nxc"]
kali-package: "netexec"
kali-version: "1.5.1-0kali2"
---

# Installing netexec

> A Windows / Active Directory environments pentest tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netexec) (version `v1.5.1.r1170.g93d62e5`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S netexec    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install netexec    # Kali package version 1.5.1-0kali2
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Pennyw0rth/NetExec.git
cd NetExec
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`, `rust`

## Exact build steps from the recipe

```bash
cd $pkgname

  virtualenv venv
  source venv/bin/activate
  pip install pyinstaller . # it does not work with system pyinstaller

  pyinstaller netexec.spec

  deactivate
```

## Installed layout (per recipe)

- Commands: `/usr/bin/nxc`

Backlinks: [[scanner]] · [[netexec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#netexec    # ad-hoc run, no install
nix profile install nixpkgs#netexec   # or persist it
```
