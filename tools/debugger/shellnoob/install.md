---
tool: "shellnoob"
build-system: "scripts"
upstream: "https://github.com/reyammer/shellnoob"
recipe: "blackarch"
nix-package: "shellnoob"
aur-package: "shellnoob"
source: "https://github.com/reyammer/shellnoob"
license: "MIT"
blackarch-pkgver: "37.6586133"
provides: ["shellnoob"]
kali-package: "shellnoob"
kali-version: "2.1+git20170425-0kali5"
---

# Installing shellnoob

> A toolkit that eases the writing and debugging of shellcode.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shellnoob) (version `37.6586133`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shellnoob    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install shellnoob    # Kali package version 2.1+git20170425-0kali5
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/reyammer/shellnoob) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/shellnoob`

Backlinks: [[debugger]] · [[shellnoob]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#shellnoob    # ad-hoc run, no install
nix profile install nixpkgs#shellnoob   # or persist it
```
