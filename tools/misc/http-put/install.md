---
tool: "http-put"
build-system: "perl"
upstream: ""
recipe: "blackarch"
source: "http://examples.oreilly.com/networksa/tools/put.pl"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing http-put

> Simple http put perl script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/http-put) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S http-put    # needs the [blackarch] repo enabled
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
  - `perl-net-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[http-put]] · [[BlackArch Tools]]

