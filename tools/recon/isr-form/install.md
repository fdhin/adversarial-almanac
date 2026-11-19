---
tool: "isr-form"
build-system: "perl"
upstream: "http://www.infobyte.com.ar/"
recipe: "blackarch"
source: "http://www.infobyte.com.ar/"
license: "custom"
blackarch-pkgver: "1.0"
---

# Installing isr-form

> Simple html parsing tool that extracts all form related information and generates reports of the data. Allows for quick analyzing of data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/isr-form) (version `1.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S isr-form    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.infobyte.com.ar/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-data-dump`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[isr-form]] · [[BlackArch Tools]]

