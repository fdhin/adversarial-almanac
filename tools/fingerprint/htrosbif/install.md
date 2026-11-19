---
tool: "htrosbif"
build-system: "perl"
upstream: "https://github.com/lkarsten/htrosbif"
recipe: "blackarch"
source: "https://github.com/lkarsten/htrosbif"
license: "GPL-3.0-or-later"
blackarch-pkgver: "134.9dc3f86"
---

# Installing htrosbif

> Active HTTP server fingerprinting and recon tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/htrosbif) (version `134.9dc3f86`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S htrosbif    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/lkarsten/htrosbif) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-json-xs`
  - `perl-list-moreutils`
  - `perl-test-deep`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[htrosbif]] · [[BlackArch Tools]]

