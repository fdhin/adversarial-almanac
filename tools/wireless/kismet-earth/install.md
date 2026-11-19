---
tool: "kismet-earth"
build-system: "perl"
upstream: "https://www.blackarch.org/"
recipe: "blackarch"
source: "https://www.blackarch.org/"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing kismet-earth

> Various scripts to convert kismet logs to kml file to be used in Google Earth.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kismet-earth) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S kismet-earth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.blackarch.org/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[kismet-earth]] · [[BlackArch Tools]]

