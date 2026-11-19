---
tool: "tiger"
build-system: "perl"
upstream: "http://www.nongnu.org/tiger/"
recipe: "blackarch"
aur-package: "tiger"
source: "http://www.nongnu.org/tiger/"
license: "GPL"
blackarch-pkgver: "3.2.3"
kali-package: "tiger"
kali-version: "1:3.2.4~rc1-3.5"
---

# Installing tiger

> A security scanner, that checks computer for known problems. Can also use tripwire, aide and chkrootkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tiger) (version `3.2.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tiger    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install tiger    # Kali package version 1:3.2.4~rc1-3.5
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.nongnu.org/tiger/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `patch`
- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[tiger]] · [[BlackArch Tools]]

