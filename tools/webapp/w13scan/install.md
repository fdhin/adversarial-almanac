---
tool: "w13scan"
build-system: "scripts"
upstream: "https://github.com/boy-hack/w13scan"
recipe: "blackarch"
source: "https://github.com/boy-hack/w13scan"
license: "GPL2"
blackarch-pkgver: "430.432b835"
---

# Installing w13scan

> Passive Security Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/w13scan) (version `430.432b835`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S w13scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/boy-hack/w13scan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `certifi`, `cffi`, `chardet`, `colorama`, `cowpy`, `cryptography`, `cssselect`, `idna`, `lxml`, `pycparser`, `pyjsparser`, `pyopenssl`, `pysocks`, `requests`, `setuptools`, `six`, `tld`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[w13scan]] · [[BlackArch Tools]]

