---
tool: "praeda"
build-system: "perl"
upstream: "https://github.com/percx/Praeda"
recipe: "blackarch"
source: "https://github.com/percx/Praeda"
license: "GPL2"
blackarch-pkgver: "49.b0b17d3"
---

# Installing praeda

> An automated data/information harvesting tool designed to gather critical information from various embedded devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/praeda) (version `49.b0b17d3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S praeda    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/percx/Praeda.git
cd Praeda
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-crypt-ssleay`
  - `perl-html-tableextract`
  - `perl-html-tagparser`
  - `perl-http-cookies`
  - `perl-io-socket`
  - `perl-net-snmp`
  - `perl-net-ssleay`
  - `perl-netaddr-ip`
  - `perl-uri-fetch`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[praeda]] · [[BlackArch Tools]]

