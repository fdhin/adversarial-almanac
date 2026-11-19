---
tool: "siparmyknife"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/107301/sipArmyKnife_11232011.pl.txt"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/107301/sipArmyKnife_11232011.pl.txt"
license: "GPL"
blackarch-pkgver: "11232011"
kali-package: "siparmyknife"
kali-version: "11232011-1kali3"
---

# Installing siparmyknife

> A small command line tool for developers and administrators of Session Initiation Protocol (SIP) applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/siparmyknife) (version `11232011`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S siparmyknife    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install siparmyknife    # Kali package version 11232011-1kali3
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/107301/sipArmyKnife_11232011.pl.txt) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-digest-crc`
  - `perl-digest-md4`
  - `perl-digest-md5`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[siparmyknife]] · [[BlackArch Tools]]

