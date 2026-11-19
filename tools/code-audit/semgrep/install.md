---
tool: "semgrep"
build-system: "scripts"
upstream: "https://pypi.org/project/semgrep/"
recipe: "blackarch"
nix-package: "semgrep"
source: "https://pypi.org/project/semgrep/"
license: "LGPL-2.0-only"
blackarch-pkgver: "1.183.0"
---

# Installing semgrep

> Lightweight static analysis for many languages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/semgrep) (version `1.183.0`, license LGPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S semgrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/semgrep/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`, `virtualenv`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[semgrep]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#semgrep    # ad-hoc run, no install
nix profile install nixpkgs#semgrep   # or persist it
```
