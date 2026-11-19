---
tool: "ikecrack"
build-system: "perl"
upstream: "http://sourceforge.net/projects/ikecrack/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/ikecrack/"
license: "custom:unknown"
blackarch-pkgver: "1.00"
provides: ["ikecrack-snarf"]
---

# Installing ikecrack

> An IKE/IPSec crack tool designed to perform Pre-Shared-Key analysis of RFC compliant aggressive mode authentication

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ikecrack) (version `1.00`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ikecrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/ikecrack/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-digest-hmac`

## Installed layout (per recipe)

- Commands: `/usr/bin/ikecrack-snarf`

Backlinks: [[cracker]] · [[ikecrack]] · [[BlackArch Tools]]

