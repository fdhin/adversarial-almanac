---
tool: "dnspredict"
build-system: "perl"
upstream: "http://johnny.ihackstuff.com/"
recipe: "blackarch"
source: "http://johnny.ihackstuff.com/"
license: "GPL"
blackarch-pkgver: "0.0.2"
---

# Installing dnspredict

> DNS prediction.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnspredict) (version `0.0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dnspredict    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://johnny.ihackstuff.com/) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[dnspredict]] · [[BlackArch Tools]]

