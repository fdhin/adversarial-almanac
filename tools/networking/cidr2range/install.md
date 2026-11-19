---
tool: "cidr2range"
build-system: "perl"
upstream: "http://www.cpan.org/authors/id/R/RA/RAYNERLUC"
recipe: "blackarch"
source: "http://www.cpan.org/authors/id/R/RA/RAYNERLUC/cidr2range/cidr2range-1.0.pl"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing cidr2range

> Script for listing the IP addresses contained in a CIDR netblock.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cidr2range) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cidr2range    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.cpan.org/authors/id/R/RA/RAYNERLUC) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-cidr`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[cidr2range]] · [[BlackArch Tools]]

