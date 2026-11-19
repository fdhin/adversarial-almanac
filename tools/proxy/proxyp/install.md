---
tool: "proxyp"
build-system: "perl"
upstream: "http://sourceforge.net/projects/proxyp/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/proxyp/"
license: "GPL3"
blackarch-pkgver: "2013"
---

# Installing proxyp

> Small multithreaded Perl script written to enumerate latency, port numbers, server names, & geolocations of proxy IP addresses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxyp) (version `2013`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S proxyp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/proxyp/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-ip-country`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[proxyp]] · [[BlackArch Tools]]

