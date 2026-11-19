---
tool: "vbscan"
build-system: "perl"
upstream: "https://github.com/rezasp/vbscan"
recipe: "blackarch"
source: "https://github.com/rezasp/vbscan"
license: "GPL3"
blackarch-pkgver: "39.2b1ce48"
---

# Installing vbscan

> A black box vBulletin vulnerability scanner written in perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vbscan) (version `39.2b1ce48`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S vbscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/rezasp/vbscan) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[vbscan]] · [[BlackArch Tools]]

