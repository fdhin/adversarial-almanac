---
tool: "seat"
build-system: "perl"
upstream: "http://thesprawl.org/projects/search-engine-assessment-tool/"
recipe: "blackarch"
source: "http://thesprawl.org/projects/search-engine-assessment-tool/"
license: "GPL2"
blackarch-pkgver: "0.3"
---

# Installing seat

> Next generation information digging application geared toward the needs of security professionals. It uses information stored in search engine databases, cache repositories, and other public resources to scan web sites for potential vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seat) (version `0.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S seat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://thesprawl.org/projects/search-engine-assessment-tool/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gtk2`
  - `gtk2-perl`
  - `perl`
  - `perl-object-multitype`
  - `perl-xml-smart`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[seat]] · [[BlackArch Tools]]

