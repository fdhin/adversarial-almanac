---
tool: "metaforge"
build-system: "perl"
upstream: "https://github.com/chriswmorris/Metaforge"
recipe: "blackarch"
source: "https://github.com/chriswmorris/Metaforge"
license: "GPL3"
blackarch-pkgver: "115.7b32693"
---

# Installing metaforge

> Auto Scanning to SSL Vulnerability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metaforge) (version `115.7b32693`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S metaforge    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/chriswmorris/Metaforge.git
cd Metaforge
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `dominate`, `progress`, `pyexifinfo`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl-image-exiftool`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[metaforge]] · [[BlackArch Tools]]

