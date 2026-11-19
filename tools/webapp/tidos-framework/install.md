---
tool: "tidos-framework"
build-system: "scripts"
upstream: "https://github.com/0xInfection/TIDoS-Framework"
recipe: "blackarch"
source: "https://github.com/0xInfection/TIDoS-Framework"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v2.0.beta2.r22.g4098187"
provides: ["tidconsole", "tidv2"]
---

# Installing tidos-framework

> Offensive Web Application Penetration Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tidos-framework) (version `v2.0.beta2.r22.g4098187`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S tidos-framework    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xInfection/TIDoS-Framework) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bs4`, `builtwith`, `colorama`, `dnslib`, `emailprotectionslib`, `google`, `idna`, `lxml`, `mechanize`, `mysql-connector`, `mysqlclient`, `pexpect`, `pillow`, `pip`, `ptyprocess`, `pyqt5`, `requests`, `scapy`, `shodan`, `six`, `texttable`, `tld`, `tldextract`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `exiv2`
  - `konsole`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `mariadb`
  - `ncurses`
  - `nmap`
  - `photon`
  - `python`
  - `sslyze`
  - `tcpdump`
  - `tor`

## Installed layout (per recipe)

- Commands: `/usr/bin/tidconsole`, `/usr/bin/tidv2`

Backlinks: [[webapp]] · [[tidos-framework]] · [[BlackArch Tools]]

