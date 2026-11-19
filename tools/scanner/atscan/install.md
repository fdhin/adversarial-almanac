---
tool: "atscan"
build-system: "perl"
upstream: "https://github.com/AlisamTechnology/ATSCAN-V3.1"
recipe: "blackarch"
source: "https://github.com/AlisamTechnology/ATSCAN-V3.1"
license: "MIT"
blackarch-pkgver: "2461.06521a9"
---

# Installing atscan

> Server, Site and Dork Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/atscan) (version `2461.06521a9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S atscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/AlisamTechnology/ATSCAN-V3.1.git
cd ATSCAN-V3.1
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `perl`
  - `perl-encode-locale`
  - `perl-file-copy-recursive`
  - `perl-getopt-long`
  - `perl-html-parser`
  - `perl-http-cookies`
  - `perl-http-message`
  - `perl-io-socket`
  - `perl-libwww`
  - `perl-term-ansicolor`
  - `perl-term-readkey`
  - `perl-uri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[atscan]] · [[BlackArch Tools]]

