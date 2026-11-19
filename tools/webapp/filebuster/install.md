---
tool: "filebuster"
build-system: "perl"
upstream: "https://github.com/henshin/filebuster"
recipe: "blackarch"
source: "https://github.com/henshin/filebuster"
license: "GPL3"
blackarch-pkgver: "95.f2b04c7"
---

# Installing filebuster

> An extremely fast and flexible web fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/filebuster) (version `95.f2b04c7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S filebuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/henshin/filebuster) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-cache-lru`
  - `perl-class-accessor-lite`
  - `perl-exporter-tiny`
  - `perl-furl`
  - `perl-html-parser`
  - `perl-http-parser-xs`
  - `perl-io-async`
  - `perl-io-socket-socks-wrapper`
  - `perl-io-socket-ssl`
  - `perl-list-moreutils`
  - `perl-net-dns`
  - `perl-net-dns-lite`
  - `perl-switch`
  - `perl-uri`
  - `perl-yaml`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[filebuster]] · [[BlackArch Tools]]

