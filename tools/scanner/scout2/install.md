---
tool: "scout2"
build-system: "scripts"
upstream: "http://isecpartners.github.io/Scout2/"
recipe: "blackarch"
source: "http://isecpartners.github.io/Scout2/"
license: "GPL2"
blackarch-pkgver: "1182.5d86d46"
---

# Installing scout2

> Security auditing tool for AWS environments.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scout2) (version `1182.5d86d46`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S scout2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/iSECPartners/Scout2.git
cd Scout2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-boto3, python2-botocore, python2-dateutil, python2-iampoliciesgonewild, python2-jmespath, python2-netaddr, python2-opinel (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[scout2]] · [[BlackArch Tools]]

