---
tool: "smtpscan"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/31102/smtpscan-${pkgver}.tar.gz.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/31102/smtpscan-${pkgver}.tar.gz.html"
license: "GPL2"
blackarch-pkgver: "0.5"
---

# Installing smtpscan

> An SMTP scanner

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtpscan) (version `0.5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S smtpscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/31102/smtpscan.5.tar.gz.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-dns`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[fingerprint]] · [[smtpscan]] · [[BlackArch Tools]]

