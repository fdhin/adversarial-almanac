---
tool: "cisco-auditing-tool"
build-system: "perl"
upstream: "http://www.scrypt.net"
recipe: "blackarch"
source: "http://www.scrypt.net"
license: "GPL"
blackarch-pkgver: "1"
provides: ["CAT"]
kali-package: "cisco-auditing-tool"
kali-version: "1.0-1kali6"
---

# Installing cisco-auditing-tool

> Perl script which scans cisco routers for common vulnerabilities. Checks for default passwords, easily guessable community names, and the IOS history bug. Includes support for plugins and scanning multiple hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco-auditing-tool) (version `1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cisco-auditing-tool    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cisco-auditing-tool    # Kali package version 1.0-1kali6
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.scrypt.net) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- Commands: `/usr/bin/CAT`

Backlinks: [[cracker]] · [[cisco-auditing-tool]] · [[BlackArch Tools]]

