---
tool: "aws-inventory"
build-system: "scripts"
upstream: "https://github.com/nccgroup/aws-inventory"
recipe: "blackarch"
source: "https://github.com/nccgroup/aws-inventory"
license: "AGPL"
blackarch-pkgver: "19.9a2fa8e"
---

# Installing aws-inventory

> Discover resources created in an AWS account.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aws-inventory) (version `19.9a2fa8e`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S aws-inventory    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/aws-inventory) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `tk`
- ⚠️ Python 2 only deps: python2-argparse, python2-botocore, python2-opinel (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[aws-inventory]] · [[BlackArch Tools]]

