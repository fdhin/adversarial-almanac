---
tool: "altdns"
build-system: "python"
upstream: "https://github.com/infosec-au/altdns"
recipe: "blackarch"
nix-package: "altdns"
source: "https://github.com/infosec-au/altdns"
license: "Apache-2.0"
blackarch-pkgver: "78.6728272"
---

# Installing altdns

> Generates permutations, alterations and mutations of subdomains and then resolves them.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/altdns) (version `78.6728272`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S altdns    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/infosec-au/altdns) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `setuptools`, `termcolor`, `tldextract`
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

Backlinks: [[recon]] · [[altdns]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#altdns    # ad-hoc run, no install
nix profile install nixpkgs#altdns   # or persist it
```
