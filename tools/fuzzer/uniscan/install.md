---
tool: "uniscan"
build-system: "perl"
upstream: "http://sourceforge.net/projects/uniscan/"
recipe: "blackarch"
source: "http://downloads.sourceforge.net/project/uniscan/6.2/uniscan6.2.tar.gz"
license: "GPL2"
blackarch-pkgver: "6.3"
kali-package: "uniscan"
kali-version: "6.3-0kali3"
---

# Installing uniscan

> A simple Remote File Include, Local File Include and Remote Command Execution vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uniscan) (version `6.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S uniscan    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install uniscan    # Kali package version 6.3-0kali3
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO http://downloads.sourceforge.net/project/uniscan/6.2/uniscan6.2.tar.gz
tar xf uniscan6.2.tar.gz
cd uniscan6.2
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-devel-overloadinfo`
  - `perl-lwp-protocol-https`
  - `perl-moose`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[uniscan]] · [[BlackArch Tools]]

