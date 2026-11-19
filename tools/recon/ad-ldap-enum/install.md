---
tool: "ad-ldap-enum"
build-system: "scripts"
upstream: "https://github.com/CroweCybersecurity/ad-ldap-enum"
recipe: "blackarch"
nix-package: "ad-ldap-enum"
source: "https://github.com/CroweCybersecurity/ad-ldap-enum"
license: "MIT"
blackarch-pkgver: "90.caee43d"
---

# Installing ad-ldap-enum

> An LDAP based Active Directory user and group enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ad-ldap-enum) (version `90.caee43d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ad-ldap-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CroweCybersecurity/ad-ldap-enum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `openpyxl`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ad-ldap-enum]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ad-ldap-enum    # ad-hoc run, no install
nix profile install nixpkgs#ad-ldap-enum   # or persist it
```
