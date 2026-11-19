---
tool: "lotophagi"
build-system: "perl"
upstream: "http://packetstormsecurity.com/files/55250/lotophagi.rar.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/55250/lotophagi.rar.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing lotophagi

> a relatively compact Perl script designed to scan remote hosts for default (or common) Lotus NSF and BOX databases.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lotophagi) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lotophagi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/55250/lotophagi.rar.html) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwww`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[lotophagi]] · [[BlackArch Tools]]

