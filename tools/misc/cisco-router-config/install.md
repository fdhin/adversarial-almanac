---
tool: "cisco-router-config"
build-system: "perl"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL"
blackarch-pkgver: "1.1"
---

# Installing cisco-router-config

> Tools to copy and merge Cisco Routers Configuration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco-router-config) (version `1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cisco-router-config    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-cisco-copyconfig`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[cisco-router-config]] · [[BlackArch Tools]]

