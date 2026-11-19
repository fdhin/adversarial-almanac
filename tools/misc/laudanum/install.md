---
tool: "laudanum"
build-system: "scripts"
upstream: "http://laudanum.inguardians.com/#"
recipe: "blackarch"
nix-package: "laudanum"
source: "http://laudanum.inguardians.com/#"
license: "GPL"
blackarch-pkgver: "1.0"
kali-package: "laudanum"
kali-version: "1.0+r36-0kali6"
---

# Installing laudanum

> A collection of injectable files, designed to be used in a pentest when SQL injection flaws are found and are in multiple languages for different environments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/laudanum) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S laudanum    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install laudanum    # Kali package version 1.0+r36-0kali6
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://laudanum.inguardians.com/#) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[laudanum]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#laudanum    # ad-hoc run, no install
nix profile install nixpkgs#laudanum   # or persist it
```
