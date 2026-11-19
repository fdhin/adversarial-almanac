---
tool: "scap-security-guide"
build-system: "scripts"
upstream: "https://www.open-scap.org/security-policies/scap-security-guide/"
recipe: "blackarch"
nix-package: "scap-security-guide"
aur-package: "scap-security-guide"
source: "https://www.open-scap.org/security-policies/scap-security-guide/"
license: "BSD-3-Clause"
blackarch-pkgver: "0.1.80"
---

# Installing scap-security-guide

> Security compliance content in SCAP, Bash, Ansible, and other formats.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scap-security-guide) (version `0.1.80`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S scap-security-guide    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.open-scap.org/security-policies/scap-security-guide/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[scap-security-guide]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#scap-security-guide    # ad-hoc run, no install
nix profile install nixpkgs#scap-security-guide   # or persist it
```
