---
tool: "proxyscan"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/69778/proxyScan-0.3.tgz.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/69778/proxyScan-0.3.tgz.html"
license: "GPL2"
blackarch-pkgver: "0.3"
---

# Installing proxyscan

> A security penetration testing tool to scan for hosts and ports through a Web proxy server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxyscan) (version `0.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S proxyscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/69778/proxyScan.3.tgz.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwww`
  - `perl-lwp-useragent-proxyany`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[proxyscan]] · [[BlackArch Tools]]

