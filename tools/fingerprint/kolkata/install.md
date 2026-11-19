---
tool: "kolkata"
build-system: "perl"
upstream: "https://www.blackhatlibrary.net/Kolkata"
recipe: "blackarch"
source: "https://www.blackhatlibrary.net/Kolkata"
license: "GPL-2.0-or-later"
blackarch-pkgver: "3.0"
---

# Installing kolkata

> A web application fingerprinting engine written in Perl that combines cryptography with IDS evasion.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kolkata) (version `3.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S kolkata    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.blackhatlibrary.net/Kolkata) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwhisker2`
  - `perl-yaml`
  - `perl-yaml-libyaml`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[kolkata]] · [[BlackArch Tools]]

