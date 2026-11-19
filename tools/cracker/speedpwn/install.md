---
tool: "speedpwn"
build-system: "perl"
upstream: "https://gitorious.org/speedpwn/"
recipe: "blackarch"
source: "http://download.aircrack-ng.org/aircrack-ng-1.2-beta3.tar.gz"
license: "GPL2"
blackarch-pkgver: "8.3dd2793"
provides: ["speedport-arcadyan"]
---

# Installing speedpwn

> An active WPA/2 Bruteforcer, original created to prove weak standard key generation in different ISP labeled routers without a client is connected.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/speedpwn) (version `8.3dd2793`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S speedpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO http://download.aircrack-ng.org/aircrack-ng-1.2-beta3.tar.gz
tar xf aircrack-ng-1.2-beta3.tar.gz
cd aircrack-ng-1.2-beta3
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `perl`

## Installed layout (per recipe)

- Commands: `/usr/bin/speedport-arcadyan`

Backlinks: [[cracker]] · [[speedpwn]] · [[BlackArch Tools]]

