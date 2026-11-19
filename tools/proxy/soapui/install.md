---
tool: "soapui"
build-system: "scripts"
upstream: "https://www.soapui.org/downloads/soapui/source-forge.html"
recipe: "blackarch"
nix-package: "soapui"
aur-package: "soapui"
source: "https://www.soapui.org/downloads/soapui/source-forge.html"
license: "custom:unknown"
blackarch-pkgver: "5.8.0"
---

# Installing soapui

> The Swiss-Army Knife for SOAP Testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/soapui) (version `5.8.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S soapui    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.soapui.org/downloads/soapui/source-forge.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[soapui]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#soapui    # ad-hoc run, no install
nix profile install nixpkgs#soapui   # or persist it
```
