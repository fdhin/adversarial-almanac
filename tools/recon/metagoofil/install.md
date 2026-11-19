---
tool: "metagoofil"
build-system: "scripts"
upstream: "http://www.edge-security.com/metagoofil.php"
recipe: "blackarch"
source: "http://www.edge-security.com/metagoofil.php"
license: "GPL3"
blackarch-pkgver: "97.88e9a3c"
kali-package: "metagoofil"
kali-version: "1:1.2.0+git20221009-0kali1"
---

# Installing metagoofil

> An information gathering tool designed for extracting metadata of public documents.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metagoofil) (version `97.88e9a3c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S metagoofil    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install metagoofil    # Kali package version 1:1.2.0+git20221009-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.edge-security.com/metagoofil.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `google`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[metagoofil]] · [[BlackArch Tools]]

