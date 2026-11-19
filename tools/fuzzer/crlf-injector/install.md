---
tool: "crlf-injector"
build-system: "scripts"
upstream: "https://github.com/rudSarkar/crlf-injector"
recipe: "blackarch"
source: "https://github.com/rudSarkar/crlf-injector"
license: "custom:unknown"
blackarch-pkgver: "9.bd6db06"
---

# Installing crlf-injector

> A python script for testing CRLF injecting issues.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crlf-injector) (version `9.bd6db06`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crlf-injector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rudSarkar/crlf-injector) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-netaddr, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[crlf-injector]] · [[BlackArch Tools]]

