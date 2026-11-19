---
tool: "androidsniffer"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/97464/Andr01d-Magic-Dumper-0.1.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/97464/Andr01d-Magic-Dumper-0.1.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing androidsniffer

> A perl script that lets you search for 3rd party passwords, dump the call log, dump contacts, dump wireless configuration, and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androidsniffer) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S androidsniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/97464/Andr01d-Magic-Dumper.1.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-dbi`
  - `perl-term-menu`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[androidsniffer]] · [[BlackArch Tools]]

