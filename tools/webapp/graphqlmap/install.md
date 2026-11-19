---
tool: "graphqlmap"
build-system: "python"
upstream: "https://github.com/swisskyrepo/GraphQLmap"
recipe: "blackarch"
nix-package: "graphqlmap"
source: "https://github.com/swisskyrepo/GraphQLmap"
license: "MIT"
blackarch-pkgver: "63.59305d7"
---

# Installing graphqlmap

> Scripting engine to interact with a graphql endpoint for pentesting purposes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graphqlmap) (version `63.59305d7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S graphqlmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/swisskyrepo/GraphQLmap.git
cd GraphQLmap
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[graphqlmap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#graphqlmap    # ad-hoc run, no install
nix profile install nixpkgs#graphqlmap   # or persist it
```
