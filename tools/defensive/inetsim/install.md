---
tool: "inetsim"
build-system: "perl"
upstream: "http://www.inetsim.org/"
recipe: "blackarch"
aur-package: "inetsim"
source: "http://www.inetsim.org/"
license: "GPL3"
blackarch-pkgver: "1.3.2"
kali-package: "inetsim"
kali-version: "1.3.2+dfsg.1-8"
---

# Installing inetsim

> A software suite for simulating common internet services in a lab environment, e.g. for analyzing the network behaviour of unknown malware samples.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inetsim) (version `1.3.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S inetsim    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install inetsim    # Kali package version 1.3.2+dfsg.1-8
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.inetsim.org/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `perl`
  - `perl-ipc-shareable`
  - `perl-net-dns`
  - `perl-net-server`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[inetsim]] · [[BlackArch Tools]]

