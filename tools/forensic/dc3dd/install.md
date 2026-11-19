---
tool: "dc3dd"
build-system: "perl"
upstream: "http://sourceforge.net/projects/dc3dd"
recipe: "blackarch"
nix-package: "dc3dd"
aur-package: "dc3dd"
source: "http://sourceforge.net/projects/dc3dd"
license: "GPL3"
blackarch-pkgver: "7.2.646"
kali-package: "dc3dd"
kali-version: "7.3.1-4"
---

# Installing dc3dd

> A patched version of dd that includes a number of features useful for computer forensics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dc3dd) (version `7.2.646`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dc3dd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dc3dd    # Kali package version 7.3.1-4
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/dc3dd) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl-locale-gettext`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dc3dd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dc3dd    # ad-hoc run, no install
nix profile install nixpkgs#dc3dd   # or persist it
```
