---
tool: "objection"
build-system: "python"
upstream: "https://pypi.org/project/objection/#files"
recipe: "blackarch"
nix-package: "objection"
aur-package: "objection"
source: "https://pypi.org/project/objection/#files"
license: "custom:unknown"
blackarch-pkgver: "1.12.5"
---

# Installing objection

> Instrumented Mobile Pentest Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/objection) (version `1.12.5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S objection    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/objection/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `click`, `delegator.py`, `flask`, `frida`, `frida-tools`, `installer`, `litecli`, `packaging`, `pexpect`, `prompt_toolkit`, `pygments`, `requests`, `semver`, `setuptools`, `sqlparse`, `tabulate`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[objection]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#objection    # ad-hoc run, no install
nix profile install nixpkgs#objection   # or persist it
```
