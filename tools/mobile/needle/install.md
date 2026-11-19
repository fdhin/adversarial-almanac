---
tool: "needle"
build-system: "scripts"
upstream: "https://github.com/mwrlabs/needle"
recipe: "blackarch"
nix-package: "needle"
source: "https://github.com/mwrlabs/needle"
license: "custom:unknown"
blackarch-pkgver: "579.891b660"
---

# Installing needle

> The iOS Security Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/needle) (version `579.891b660`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S needle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mwrlabs/needle) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mitmproxy`
  - `python2`
- ⚠️ Python 2 only deps: python2-biplist, python2-frida, python2-paramiko, python2-pyopenssl, python2-pysqlite, python2-sshtunnel (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[needle]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#needle    # ad-hoc run, no install
nix profile install nixpkgs#needle   # or persist it
```
