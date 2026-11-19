---
tool: "whispers"
build-system: "python"
upstream: "https://github.com/adeptex/whispers/"
recipe: "blackarch"
nix-package: "whispers"
source: "https://github.com/adeptex/whispers/"
license: "GPL3"
blackarch-pkgver: "2.4.0.r0.g24ee0f0"
---

# Installing whispers

> Identify hardcoded secrets in static structured text.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whispers) (version `2.4.0.r0.g24ee0f0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S whispers    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/adeptex/whispers/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `astroid`, `beautifulsoup4`, `crossplane`, `jellyfish`, `jproperties`, `luhn`, `lxml`, `pip`, `setuptools`, `yaml`
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

Backlinks: [[code-audit]] · [[whispers]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#whispers    # ad-hoc run, no install
nix profile install nixpkgs#whispers   # or persist it
```
