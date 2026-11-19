---
tool: "ghettotooth"
build-system: "perl"
upstream: "http://www.oldskoolphreak.com/tfiles/ghettotooth.txt"
recipe: "blackarch"
source: "http://www.oldskoolphreak.com/tfiles/ghettotooth.txt"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing ghettotooth

> Ghettodriving for bluetooth.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghettotooth) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ghettotooth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.oldskoolphreak.com/tfiles/ghettotooth.txt) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[ghettotooth]] · [[BlackArch Tools]]

