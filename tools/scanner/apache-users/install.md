---
tool: "apache-users"
build-system: "perl"
upstream: "https://labs.portcullis.co.uk/downloads/"
recipe: "blackarch"
nix-package: "apache-users"
source: "https://labs.portcullis.co.uk/downloads/"
license: "GPL2"
blackarch-pkgver: "2.1"
kali-package: "apache-users"
kali-version: "2.1-1kali6"
---

# Installing apache-users

> This perl script will enumerate the usernames on a unix system that use the apache module UserDir.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apache-users) (version `2.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S apache-users    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install apache-users    # Kali package version 2.1-1kali6
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://labs.portcullis.co.uk/downloads/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-io-socket`
  - `perl-libwww`
  - `perl-parallel-forkmanager`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/apache-users`

Backlinks: [[scanner]] · [[apache-users]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#apache-users    # ad-hoc run, no install
nix profile install nixpkgs#apache-users   # or persist it
```
