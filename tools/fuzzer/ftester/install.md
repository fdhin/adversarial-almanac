---
tool: "ftester"
build-system: "perl"
upstream: "http://www.inversepath.com/ftester.html"
recipe: "blackarch"
source: "http://www.inversepath.com/ftester.html"
license: "GPL3"
blackarch-pkgver: "1.0"
kali-package: "ftester"
kali-version: "1.0-1kali3"
---

# Installing ftester

> A tool designed for testing firewall filtering policies and Intrusion Detection System (IDS) capabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ftester) (version `1.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ftester    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install ftester    # Kali package version 1.0-1kali3
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.inversepath.com/ftester.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-pcaputils`
  - `perl-net-rawip`
  - `perl-netpacket`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[fuzzer]] · [[ftester]] · [[BlackArch Tools]]

