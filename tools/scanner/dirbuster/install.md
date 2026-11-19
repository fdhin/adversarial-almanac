---
tool: "dirbuster"
build-system: "scripts"
upstream: "http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project"
recipe: "blackarch"
nix-package: "dirbuster"
aur-package: "dirbuster"
source: "http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project"
license: "LGPL"
blackarch-pkgver: "1.0_RC1"
kali-package: "dirbuster"
kali-version: "1.0-1kali6"
---

# Installing dirbuster

> An application designed to brute force directories and files names on web/application servers

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirbuster) (version `1.0_RC1`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S dirbuster    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dirbuster    # Kali package version 1.0-1kali6
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dirbuster]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dirbuster    # ad-hoc run, no install
nix profile install nixpkgs#dirbuster   # or persist it
```
