---
tool: "zarn"
build-system: "perl"
upstream: "https://github.com/htrgouvea/zarn"
recipe: "blackarch"
aur-package: "zarn"
source: "https://github.com/htrgouvea/zarn"
license: "MIT"
blackarch-pkgver: "0.0.9.r146.gcf91492"
---

# Installing zarn

> A lightweight static security analysis tool for modern Perl Apps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zarn) (version `0.0.9.r146.gcf91492`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S zarn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/htrgouvea/zarn) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-file-find-rule`
  - `perl-getopt-long`
  - `perl-json`
  - `perl-ppi`
  - `perl-yaml-tiny`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[zarn]] · [[BlackArch Tools]]

