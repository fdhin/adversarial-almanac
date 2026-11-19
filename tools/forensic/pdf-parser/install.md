---
tool: "pdf-parser"
build-system: "scripts"
upstream: "http://blog.didierstevens.com/programs/pdf-tools/"
recipe: "blackarch"
nix-package: "pdf-parser"
source: "http://blog.didierstevens.com/programs/pdf-tools/"
license: "custom"
blackarch-pkgver: "0.7.12"
kali-package: "pdf-parser"
kali-version: "0.7.14-0kali1"
---

# Installing pdf-parser

> Parses a PDF document to identify the fundamental elements used in the analyzed file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdf-parser) (version `0.7.12`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S pdf-parser    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pdf-parser    # Kali package version 0.7.14-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.didierstevens.com/programs/pdf-tools/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pdf-parser]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pdf-parser    # ad-hoc run, no install
nix profile install nixpkgs#pdf-parser   # or persist it
```
