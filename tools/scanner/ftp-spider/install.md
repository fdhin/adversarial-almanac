---
tool: "ftp-spider"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/35120/ftp-spider.pl.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/ftp-spider.pl"
license: "unknown"
blackarch-pkgver: "1.0"
---

# Installing ftp-spider

> FTP investigation tool - Scans ftp server for the following: reveal entire directory tree structures, detect anonymous access, detect directories with write permissions, find user specified data within repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ftp-spider) (version `1.0`, license unknown).

## Arch / BlackArch

```bash
sudo pacman -S ftp-spider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/35120/ftp-spider.pl.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-data-dumper`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ftp-spider]] · [[BlackArch Tools]]

