---
tool: "snmpscan"
build-system: "perl"
upstream: "http://www.nothink.org/perl/snmpscan/index.php"
recipe: "blackarch"
aur-package: "snmpscan"
source: "http://www.nothink.org/perl/snmpscan/index.php"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing snmpscan

> A free, multi-processes SNMP scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snmpscan) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S snmpscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.nothink.org/perl/snmpscan/index.php) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-ip`
  - `perl-net-snmp`
  - `perl-number-bytes-human`
  - `perl-uniq`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[snmpscan]] · [[BlackArch Tools]]

