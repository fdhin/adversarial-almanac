---
tool: "inquisitor"
build-system: "scripts"
upstream: "https://github.com/penafieljlm/inquisitor"
recipe: "blackarch"
source: "https://github.com/penafieljlm/inquisitor"
license: "custom:unknown"
blackarch-pkgver: "28.12a9ec1"
provides: ["inq"]
---

# Installing inquisitor

> OSINT Gathering Tool for Companies and Organizations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inquisitor) (version `28.12a9ec1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S inquisitor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/penafieljlm/inquisitor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-google-api-python-client, python2-ipwhois, python2-netaddr, python2-python-whois, python2-shodan, python2-tabulate, python2-tld, python2-unidecode, python2-unqlite, python2-validate-email (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/inq`

Backlinks: [[recon]] · [[inquisitor]] · [[BlackArch Tools]]

