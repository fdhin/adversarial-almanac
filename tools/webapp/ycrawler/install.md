---
tool: "ycrawler"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/98546/yCrawler-Web-Crawling-Utility.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/yCrawler.tar.gz"
license: "GPL2"
blackarch-pkgver: "0.1"
---

# Installing ycrawler

> A web crawler that is useful for grabbing all user supplied input related to a given website and will save the output. It has proxy and log file support.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ycrawler) (version `0.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ycrawler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/yCrawler.tar.gz
tar xf yCrawler.tar.gz
cd yCrawler
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ycrawler]] · [[BlackArch Tools]]

