---
tool: "flashscanner"
build-system: "perl"
upstream: "https://github.com/riusksk/FlashScanner"
recipe: "blackarch"
source: "https://github.com/riusksk/FlashScanner"
license: "custom:unknown"
blackarch-pkgver: "11.6815b02"
---

# Installing flashscanner

> Flash XSS Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flashscanner) (version `11.6815b02`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S flashscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
git clone https://github.com/riusksk/FlashScanner.git
cd FlashScanner
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwww`
  - `perl-uri`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[flashscanner]] · [[BlackArch Tools]]

