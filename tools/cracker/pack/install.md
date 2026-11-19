---
tool: "pack"
build-system: "scripts"
upstream: "http://thesprawl.org/projects/pack/"
recipe: "blackarch"
nix-package: "pack"
source: "http://thesprawl.org/projects/pack/"
license: "custom:unknown"
blackarch-pkgver: "0.0.4"
provides: ["maskgen", "policygen", "rulegen", "statsgen"]
kali-package: "pack"
kali-version: "0.0.4+git20191128.fd779b2-0kali3"
---

# Installing pack

> Password Analysis and Cracking Kit

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pack) (version `0.0.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pack    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pack    # Kali package version 0.0.4+git20191128.fd779b2-0kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://thesprawl.org/projects/pack/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aspell`
  - `aspell-en`
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/maskgen`, `/usr/bin/policygen`, `/usr/bin/rulegen`, `/usr/bin/statsgen`

Backlinks: [[cracker]] · [[pack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pack    # ad-hoc run, no install
nix profile install nixpkgs#pack   # or persist it
```
