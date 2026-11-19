---
tool: "xsss"
build-system: "perl"
upstream: "http://www.sven.de/xsss/"
recipe: "blackarch"
source: "http://www.sven.de/xsss/"
license: "GPL"
blackarch-pkgver: "0.40b"
---

# Installing xsss

> A brute force cross site scripting scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsss) (version `0.40b`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S xsss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.sven.de/xsss/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-www-mechanize`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsss]] · [[BlackArch Tools]]

