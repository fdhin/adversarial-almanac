---
tool: "synflood"
build-system: "perl"
upstream: "http://thesprawl.org/projects/syn-flooder/"
recipe: "blackarch"
source: "http://thesprawl.org/projects/syn-flooder/"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing synflood

> A very simply script to illustrate DoS SYN Flooding attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/synflood) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S synflood    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://thesprawl.org/projects/syn-flooder/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-rawip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[synflood]] · [[BlackArch Tools]]

