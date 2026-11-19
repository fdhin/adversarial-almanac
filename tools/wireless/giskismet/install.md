---
tool: "giskismet"
build-system: "perl"
upstream: "https://www.giskismet.org"
recipe: "blackarch"
source: "https://www.giskismet.org"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2.9ebe9d0"
---

# Installing giskismet

> A program to visually represent the Kismet data in a flexible manner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/giskismet) (version `2.9ebe9d0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S giskismet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.giskismet.org) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl-dbd-sqlite`
  - `perl-dbi`
  - `perl-xml-libxml`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[giskismet]] · [[BlackArch Tools]]

