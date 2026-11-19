---
tool: "fusil"
build-system: "scripts"
upstream: "http://bitbucket.org/haypo/fusil/wiki/Home"
recipe: "blackarch"
source: "http://bitbucket.org/haypo/fusil/wiki/Home"
license: "GPL2"
blackarch-pkgver: "1.5"
---

# Installing fusil

> A Python library used to write fuzzing programs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fusil) (version `1.5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fusil    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://bitbucket.org/haypo/fusil/wiki/Home) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ptrace (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[fusil]] · [[BlackArch Tools]]

