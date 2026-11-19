---
tool: "sinfp"
build-system: "perl"
upstream: "https://cpan.metacpan.org/authors/id/G/GO/GOMOR/"
recipe: "blackarch"
source: "https://cpan.metacpan.org/authors/id/G/GO/GOMOR/"
license: "PerlArtistic"
blackarch-pkgver: "1.24"
provides: ["site_perl"]
---

# Installing sinfp

> A full operating system stack fingerprinting suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sinfp) (version `1.24`, license PerlArtistic).

## Arch / BlackArch

```bash
sudo pacman -S sinfp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://cpan.metacpan.org/authors/id/G/GO/GOMOR/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-class-gomor`
  - `perl-data-dumper`
  - `perl-dbd-sqlite`
  - `perl-dbi`
  - `perl-digest-perl-md5`
  - `perl-io-socket-inet6`
  - `perl-net-frame`
  - `perl-net-frame-device`
  - `perl-net-frame-dump`
  - `perl-net-frame-layer-icmp6`
  - `perl-net-frame-layer-ipv6`
  - `perl-net-frame-layer-sinfp3`
  - `perl-net-frame-simple`
  - `perl-net-ipv4addr`
  - `perl-net-libdnet`
  - `perl-net-libdnet6`
  - `perl-net-netmask`
  - `perl-net-write`
  - `perl-net-write-fast`
  - `perl-parallel-forkmanager`
  - `perl-time-interval`

## Installed layout (per recipe)

- Commands: `/usr/bin/site_perl`

Backlinks: [[fingerprint]] · [[sinfp]] · [[BlackArch Tools]]

