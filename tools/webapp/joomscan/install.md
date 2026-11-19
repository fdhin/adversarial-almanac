---
tool: "joomscan"
build-system: "perl"
upstream: "http://joomscan.sourceforge.net/"
recipe: "blackarch"
nix-package: "joomscan"
aur-package: "joomscan"
source: "http://joomscan.sourceforge.net/"
license: "GPL3"
blackarch-pkgver: "83.2ea8cc7"
kali-package: "joomscan"
kali-version: "0.0.7-0kali2"
---

# Installing joomscan

> Detects file inclusion, sql injection, command execution vulnerabilities of a target Joomla! web site.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/joomscan) (version `83.2ea8cc7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S joomscan    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install joomscan    # Kali package version 0.0.7-0kali2
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://joomscan.sourceforge.net/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `perl-switch`
  - `perl-term-ansicolor`
  - `perl-www-mechanize`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[joomscan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#joomscan    # ad-hoc run, no install
nix profile install nixpkgs#joomscan   # or persist it
```
