---
tool: "xplico"
build-system: "perl"
upstream: "https://github.com/xplico/xplico/"
recipe: "blackarch"
aur-package: "xplico"
source: "https://github.com/xplico/xplico/"
license: "CC-BY-3.0, GPL-2.0-or-later"
blackarch-pkgver: "1.2.2"
kali-package: "xplico"
kali-version: "1.2.2-0kali7"
---

# Installing xplico

> Internet Traffic Decoder. Network Forensic Analysis Tool (NFAT).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xplico) (version `1.2.2`, license CC-BY-3.0, GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S xplico    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install xplico    # Kali package version 1.2.2-0kali7
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/xplico/xplico/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Python (pip)**: `httplib2`, `psycopg2`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `apache`
  - `dpkg`
  - `geoip`
  - `json-c`
  - `lame`
  - `libmariadbclient` — Debian libmariadb-dev / Fedora mariadb-devel
  - `libmaxminddb`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpqxx`
  - `ndpi`
  - `net-tools`
  - `perl`
  - `php`
  - `php-apache`
  - `php-pear`
  - `php-sqlite`
  - `recode`
  - `sox`
  - `sqlite3`
  - `tcpdump`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[xplico]] · [[BlackArch Tools]]

