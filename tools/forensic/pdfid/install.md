---
tool: "pdfid"
build-system: "scripts"
upstream: "http://blog.didierstevens.com/programs/pdf-tools/"
recipe: "blackarch"
nix-package: "pdfid"
source: "http://blog.didierstevens.com/programs/pdf-tools/"
license: "custom:unknown"
blackarch-pkgver: "0.2.10"
kali-package: "pdfid"
kali-version: "0.2.10-0kali1"
---

# Installing pdfid

> Scan a file to look for certain PDF keywords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pdfid) (version `0.2.10`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pdfid    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install pdfid    # Kali package version 0.2.10-0kali1
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

Backlinks: [[forensic]] · [[pdfid]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pdfid    # ad-hoc run, no install
nix profile install nixpkgs#pdfid   # or persist it
```
