---
tool: "plutil"
build-system: "perl"
upstream: "http://scw.us/iPhone/plutil/"
recipe: "blackarch"
source: "http://scw.us/iPhone/plutil/"
license: "custom:unknown"
blackarch-pkgver: "1.6"
---

# Installing plutil

> Converts .plist files between binary and UTF (editable) text formats.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/plutil) (version `1.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S plutil    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](http://scw.us/iPhone/plutil/) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[plutil]] · [[BlackArch Tools]]

