---
tool: "ikeprober"
build-system: "perl"
upstream: "http://ikecrack.sourceforge.net/"
recipe: "blackarch"
source: "http://ikecrack.sourceforge.net/"
license: "custom:unknown"
blackarch-pkgver: "1.12"
---

# Installing ikeprober

> Tool crafting IKE initiator packets and allowing many options to be manually set. Useful to find overflows, error conditions and identifiyng vendors

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ikeprober) (version `1.12`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ikeprober    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://ikecrack.sourceforge.net/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[ikeprober]] · [[BlackArch Tools]]

