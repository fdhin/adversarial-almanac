---
tool: "wepbuster"
build-system: "perl"
upstream: "https://code.google.com/archive/p/wepbuster/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/wepbuster/"
license: "BSD"
blackarch-pkgver: "1.0_beta_0.7"
---

# Installing wepbuster

> Script for automating aircrack-ng.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wepbuster) (version `1.0_beta_0.7`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S wepbuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/wepbuster/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `perl-algorithm-permute`
  - `perl-expect`
  - `perl-file-slurp`
  - `perl-number-range`
  - `perl-term-readkey`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wepbuster]] · [[BlackArch Tools]]

