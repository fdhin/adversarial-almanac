---
tool: "forkingportscanner"
build-system: "perl"
upstream: "http://magikh0e.xyz/"
recipe: "blackarch"
source: "http://magikh0e.xyz/"
license: "custom:unknown"
blackarch-pkgver: "1"
---

# Installing forkingportscanner

> Simple and fast forking port scanner written in perl. Can only scan on host at a time, the forking is done on the specified port range. Or on the default range of 1. Has the ability to scan UDP or TCP, defaults to tcp.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/forkingportscanner) (version `1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S forkingportscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://magikh0e.xyz/) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[forkingportscanner]] · [[BlackArch Tools]]

