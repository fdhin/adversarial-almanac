---
tool: "nipe"
build-system: "perl"
upstream: "https://github.com/GouveaHeitor/nipe"
recipe: "blackarch"
source: "https://github.com/GouveaHeitor/nipe"
license: "MIT"
blackarch-pkgver: "302.9e628df"
---

# Installing nipe

> A script to make Tor Network your default gateway.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nipe) (version `302.9e628df`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nipe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/GouveaHeitor/nipe) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-config-simple`
  - `perl-json`
  - `perl-libwww`
  - `perl-switch`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[nipe]] · [[BlackArch Tools]]

