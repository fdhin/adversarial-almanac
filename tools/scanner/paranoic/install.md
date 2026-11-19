---
tool: "paranoic"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/128065/Paranoic-Scan-1.7.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/128065/Paranoic-Scan-1.7.html"
license: "PerlArtistic"
blackarch-pkgver: "1.7"
---

# Installing paranoic

> A simple vulnerability scanner written in Perl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/paranoic) (version `1.7`, license PerlArtistic).

## Arch / BlackArch

```bash
sudo pacman -S paranoic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/128065/Paranoic-Scan.7.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `perl`
  - `perl-color-output`
  - `perl-dbd-mysql`
  - `perl-dbi`
  - `perl-html-form`
  - `perl-image-exiftool`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[paranoic]] · [[BlackArch Tools]]

