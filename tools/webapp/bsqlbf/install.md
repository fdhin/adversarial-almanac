---
tool: "bsqlbf"
build-system: "perl"
upstream: "http://code.google.com/p/bsqlbf-v2/"
recipe: "blackarch"
source: "http://code.google.com/p/bsqlbf-v2/"
license: "GPL"
blackarch-pkgver: "2.7"
---

# Installing bsqlbf

> Blind SQL Injection Brute Forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bsqlbf) (version `2.7`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bsqlbf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://code.google.com/p/bsqlbf-v2/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-getopt-long`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[bsqlbf]] · [[BlackArch Tools]]

