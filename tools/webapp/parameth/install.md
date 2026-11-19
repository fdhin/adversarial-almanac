---
tool: "parameth"
build-system: "scripts"
upstream: "https://github.com/mak-/parameth"
recipe: "blackarch"
source: "https://github.com/mak-/parameth"
license: "custom:unknonw"
blackarch-pkgver: "56.8da6f27"
---

# Installing parameth

> This tool can be used to brute discover GET and POST parameters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/parameth) (version `56.8da6f27`, license custom:unknonw).

## Arch / BlackArch

```bash
sudo pacman -S parameth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mak-/parameth) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-fuzzywuzzy, python2-numpy, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[parameth]] · [[BlackArch Tools]]

