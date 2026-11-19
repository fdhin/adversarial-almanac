---
tool: "webacoo"
build-system: "perl"
upstream: "https://bechtsoudis.com/webacoo/"
recipe: "blackarch"
source: "https://bechtsoudis.com/webacoo/"
license: "GPL3"
blackarch-pkgver: "0.2.3"
kali-package: "webacoo"
kali-version: "0.2.3-1kali3"
---

# Installing webacoo

> Web Backdoor Cookie Script-Kit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webacoo) (version `0.2.3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S webacoo    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install webacoo    # Kali package version 0.2.3-1kali3
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/anestisb/WeBaCoo.git
cd WeBaCoo
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket-socks`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[webacoo]] · [[BlackArch Tools]]

