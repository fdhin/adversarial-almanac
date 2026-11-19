---
tool: "bluediving"
build-system: "perl"
upstream: "http://bluediving.sourceforge.net/"
recipe: "blackarch"
source: "http://bluediving.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.9"
provides: ["bluediving-bdaddr", "bluedivingNG.pl"]
---

# Installing bluediving

> A Bluetooth penetration testing suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluediving) (version `0.9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bluediving    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://bluediving.sourceforge.net/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-utils`
  - `expat`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `obexftp`
  - `openobex`
  - `perl-xml-simple`
  - `readline`
  - `sox`

## Installed layout (per recipe)

- Commands: `/usr/bin/bluediving-bdaddr`, `/usr/bin/bluedivingNG.pl`
- Data: `/usr/share/bluediving`

Backlinks: [[bluetooth]] · [[bluediving]] · [[BlackArch Tools]]

