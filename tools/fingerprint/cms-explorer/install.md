---
tool: "cms-explorer"
build-system: "perl"
upstream: "https://github.com/FlorianHeigl/cms-explorer"
recipe: "blackarch"
source: "https://github.com/FlorianHeigl/cms-explorer"
license: "GPL3"
blackarch-pkgver: "15.23b58cd"
---

# Installing cms-explorer

> Designed to reveal the specific modules, plugins, components and themes that various cms driven websites are running.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cms-explorer) (version `15.23b58cd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cms-explorer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://github.com/FlorianHeigl/cms-explorer) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-libwhisker2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[cms-explorer]] · [[BlackArch Tools]]

