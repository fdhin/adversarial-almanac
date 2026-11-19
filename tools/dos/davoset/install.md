---
tool: "davoset"
build-system: "perl"
upstream: "http://websecurity.com.ua/davoset/"
recipe: "blackarch"
source: "http://websecurity.com.ua/davoset/"
license: "custom:unknown"
blackarch-pkgver: "1.3.7"
---

# Installing davoset

> A tool for using Abuse of Functionality and XML External Entities vulnerabilities on some websites to attack other websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/davoset) (version `1.3.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S davoset    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://websecurity.com.ua/davoset/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `perl`
  - `perl-io-socket-socks`
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[davoset]] · [[BlackArch Tools]]

