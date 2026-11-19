---
tool: "webrute"
build-system: "perl"
upstream: "https://github.com/BlackArch/webrute"
recipe: "blackarch"
source: "https://github.com/BlackArch/webrute"
license: "custom:unknown"
blackarch-pkgver: "3.3"
---

# Installing webrute

> Web server directory brute forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webrute) (version `3.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S webrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/webrute) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[webrute]] · [[BlackArch Tools]]

