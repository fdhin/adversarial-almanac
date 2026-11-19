---
tool: "strutscan"
build-system: "perl"
upstream: "https://github.com/riusksk/StrutScan"
recipe: "blackarch"
source: "https://github.com/riusksk/StrutScan"
license: "custom:unknown"
blackarch-pkgver: "4.8712c12"
---

# Installing strutscan

> Apache Struts2 vulnerability scanner written in Perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/strutscan) (version `4.8712c12`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S strutscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/riusksk/StrutScan.git
cd StrutScan
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-http-cookies`
  - `perl-libwww`
  - `perl-uri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[strutscan]] · [[BlackArch Tools]]

