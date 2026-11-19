---
tool: "gdir.pl"
build-system: "perl"
upstream: "https://gitlab.com/GasparVardanyan/gdir.pl"
recipe: "blackarch"
source: "https://gitlab.com/GasparVardanyan/gdir.pl"
license: "custom:unknown"
blackarch-pkgver: "9.d2cc05d"
---

# Installing gdir.pl

> Perl wrapper on gcrypt for directory encryption/decryption.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gdir.pl) (version `9.d2cc05d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gdir.pl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://gitlab.com/GasparVardanyan/gdir.pl) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcrypt`
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[gdir.pl]] · [[BlackArch Tools]]

