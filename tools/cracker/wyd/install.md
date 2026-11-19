---
tool: "wyd"
build-system: "perl"
upstream: "http://www.remote-exploit.org/?page_id=418"
recipe: "blackarch"
source: "http://www.remote-exploit.org/?page_id=418"
license: "GPL"
blackarch-pkgver: "0.2"
---

# Installing wyd

> Gets keywords from personal files. IT security/forensic tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wyd) (version `0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S wyd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://www.remote-exploit.org/?page_id=418) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[wyd]] · [[BlackArch Tools]]

