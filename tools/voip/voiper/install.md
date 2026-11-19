---
tool: "voiper"
build-system: "scripts"
upstream: "http://voiper.sourceforge.net/"
recipe: "blackarch"
source: "http://voiper.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.07"
---

# Installing voiper

> A VoIP security testing toolkit incorporating several VoIP fuzzers and auxiliary tools to assist the auditor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/voiper) (version `0.07`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S voiper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://voiper.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `wxpython`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[voiper]] · [[BlackArch Tools]]

