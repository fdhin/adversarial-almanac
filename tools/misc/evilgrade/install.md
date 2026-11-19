---
tool: "evilgrade"
build-system: "perl"
upstream: "https://github.com/infobyte/evilgrade"
recipe: "blackarch"
aur-package: "evilgrade"
source: "https://github.com/infobyte/evilgrade"
license: "GPL"
blackarch-pkgver: "2.0.9"
---

# Installing evilgrade

> Modular framework that takes advantage of poor upgrade implementations by injecting fake updates.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evilgrade) (version `2.0.9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S evilgrade    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/infobyte/evilgrade) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-data-dump`
  - `perl-digest-sha1`
  - `perl-io-socket-ssl`
  - `perl-rpc-xml`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[evilgrade]] · [[BlackArch Tools]]

