---
tool: "chaosreader"
build-system: "perl"
upstream: "http://chaosreader.sourceforge.net/"
recipe: "blackarch"
source: "http://chaosreader.sourceforge.net/"
license: "custom:unknown"
blackarch-pkgver: "0.94"
---

# Installing chaosreader

> A freeware tool to trace tcp, udp etc. sessions and fetch application data from snoop or tcpdump logs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chaosreader) (version `0.94`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S chaosreader    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://chaosreader.sourceforge.net/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[chaosreader]] · [[BlackArch Tools]]

