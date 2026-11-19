---
tool: "lbmap"
build-system: "perl"
upstream: "https://github.com/wireghoul/lbmap"
recipe: "blackarch"
source: "https://github.com/wireghoul/lbmap"
license: "GPL3"
blackarch-pkgver: "147.2d15ace"
provides: ["aprof"]
---

# Installing lbmap

> Proof of concept scripts for advanced web application fingerprinting, presented at OWASP AppSecAsia 2012.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lbmap) (version `147.2d15ace`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S lbmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/wireghoul/lbmap) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-data-dump`
  - `perl-getopt-long`
  - `perl-io-socket-ssl`

## Installed layout (per recipe)

- Commands: `/usr/bin/aprof`

Backlinks: [[fingerprint]] · [[lbmap]] · [[BlackArch Tools]]

