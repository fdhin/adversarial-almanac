---
tool: "darkjumper"
build-system: "perl"
upstream: "http://sourceforge.net/projects/darkjumper/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/darkjumper/"
license: "GPL"
blackarch-pkgver: "5.8"
---

# Installing darkjumper

> This tool will try to find every website that host at the same server at your target.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/darkjumper) (version `5.8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S darkjumper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/darkjumper/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[darkjumper]] · [[BlackArch Tools]]

