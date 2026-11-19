---
tool: "blueprint"
build-system: "perl"
upstream: "https://trifinite.org/trifinite_stuff_blueprinting.html"
recipe: "blackarch"
source: "https://trifinite.org/Downloads/bp_v01-3.zip"
license: "GPL"
blackarch-pkgver: "0.1_3"
---

# Installing blueprint

> A perl tool to identify Bluetooth devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blueprint) (version `0.1_3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S blueprint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

```bash
curl -fLO https://trifinite.org/Downloads/bp_v01-3.zip
tar xf bp_v01-3.zip
cd bp_v01-3
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-switch`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[blueprint]] · [[BlackArch Tools]]

