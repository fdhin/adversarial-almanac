---
tool: "http-fuzz"
build-system: "perl"
upstream: "none"
recipe: "blackarch"
source: "http://data.gpo.zugaina.org/pentoo/app-fuzz/http-fuzz/files/http-fuzz.pl"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing http-fuzz

> A simple http fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/http-fuzz) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S http-fuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the none (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[http-fuzz]] · [[BlackArch Tools]]

