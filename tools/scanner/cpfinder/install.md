---
tool: "cpfinder"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/118851/Control-Panel-Finder-Script.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/cpfinder.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing cpfinder

> Simple script that looks for administrative web interfaces.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cpfinder) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cpfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/scanners/cpfinder.zip
tar xf cpfinder.zip
cd cpfinder
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-http-message`
  - `perl-libwww`
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cpfinder]] · [[BlackArch Tools]]

