---
tool: "tell-me-your-secrets"
build-system: "python"
upstream: "https://github.com/valayDave/tell-me-your-secrets"
recipe: "blackarch"
nix-package: "tell-me-your-secrets"
source: "https://github.com/valayDave/tell-me-your-secrets"
license: "MIT"
blackarch-pkgver: "v2.4.2.r3.g5434b9d"
---

# Installing tell-me-your-secrets

> Find secrets on any machine from over 120 Different Signatures.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tell-me-your-secrets) (version `v2.4.2.r3.g5434b9d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tell-me-your-secrets    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/valayDave/tell-me-your-secrets) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `gitignore-parser`, `pandas`, `pip`, `wheel`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[tell-me-your-secrets]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#tell-me-your-secrets    # ad-hoc run, no install
nix profile install nixpkgs#tell-me-your-secrets   # or persist it
```
