---
tool: "gggooglescan"
build-system: "perl"
upstream: "http://www.morningstarsecurity.com/research/gggooglescan"
recipe: "blackarch"
source: "http://www.morningstarsecurity.com/research/gggooglescan"
license: "GPL3"
blackarch-pkgver: "0.4"
---

# Installing gggooglescan

> A Google scraper which performs automated searches and returns results of search queries in the form of URLs or hostnames.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gggooglescan) (version `0.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gggooglescan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.morningstarsecurity.com/research/gggooglescan) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `curl`
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gggooglescan]] · [[BlackArch Tools]]

