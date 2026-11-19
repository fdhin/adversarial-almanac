---
tool: "pulledpork"
build-system: "perl"
upstream: "https://github.com/shirkdog/pulledpork"
recipe: "blackarch"
aur-package: "pulledpork"
source: "https://github.com/shirkdog/pulledpork"
license: "GPL2"
blackarch-pkgver: "397.5ccf5c5"
---

# Installing pulledpork

> Snort rule management.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pulledpork) (version `397.5ccf5c5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pulledpork    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/shirkdog/pulledpork) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-bytes-random-secure`
  - `perl-crypt-ssleay`
  - `perl-lwp-protocol-https`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pulledpork]] · [[BlackArch Tools]]

