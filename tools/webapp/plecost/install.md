---
tool: "plecost"
build-system: "scripts"
upstream: "https://github.com/iniqua/plecost"
recipe: "blackarch"
nix-package: "plecost"
source: "https://github.com/iniqua/plecost"
license: "BSD"
blackarch-pkgver: "323.5dc64ad"
---

# Installing plecost

> Wordpress finger printer Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/plecost) (version `323.5dc64ad`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S plecost    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/iniqua/plecost) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `beautifulsoup4`, `chardet`, `lxml`, `setuptools`, `simplejson`, `sphinx_rtd_theme`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[plecost]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#plecost    # ad-hoc run, no install
nix profile install nixpkgs#plecost   # or persist it
```
