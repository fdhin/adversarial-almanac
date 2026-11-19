---
tool: "fgscanner"
build-system: "perl"
upstream: "http://www.fantaghost.com/fgscanner"
recipe: "blackarch"
source: "http://www.fantaghost.com/fgscanner"
license: "GPL3"
blackarch-pkgver: "11.893372c"
provides: ["fgscan"]
---

# Installing fgscanner

> An advanced, opensource URL scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fgscanner) (version `11.893372c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fgscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/FantaGhost/FGscanner.git
cd FGscanner
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-getopt-long`
  - `perl-io-socket-portstate`
  - `perl-libwww`

## Installed layout (per recipe)

- Commands: `/usr/bin/fgscan`

Backlinks: [[scanner]] · [[fgscanner]] · [[BlackArch Tools]]

