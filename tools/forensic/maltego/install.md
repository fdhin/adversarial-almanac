---
tool: "maltego"
build-system: "scripts"
upstream: "https://www.maltego.com/downloads/"
recipe: "blackarch"
nix-package: "maltego"
aur-package: "maltego"
source: "https://www.maltego.com/downloads/"
license: "GPL"
blackarch-pkgver: "4.14.0"
---

# Installing maltego

> An open source intelligence and forensics application, enabling to easily gather information about DNS, domains, IP addresses, websites, persons, etc.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maltego) (version `4.14.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S maltego    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.maltego.com/downloads/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `giflib`
  - `java-environment=11`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[maltego]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#maltego    # ad-hoc run, no install
nix profile install nixpkgs#maltego   # or persist it
```
