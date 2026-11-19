---
tool: "ntlmrecon"
build-system: "python"
upstream: "https://github.com/sachinkamath/ntlmrecon"
recipe: "blackarch"
nix-package: "ntlmrecon"
aur-package: "ntlmrecon"
source: "https://github.com/sachinkamath/ntlmrecon"
license: "MIT"
blackarch-pkgver: "81.64d7f9d"
---

# Installing ntlmrecon

> A tool to enumerate information from NTLM authentication enabled web endpoints.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntlmrecon) (version `81.64d7f9d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ntlmrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sachinkamath/ntlmrecon) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `iptools`, `ordered-set`, `requests`, `setuptools`, `termcolor`
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

Backlinks: [[scanner]] · [[ntlmrecon]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ntlmrecon    # ad-hoc run, no install
nix profile install nixpkgs#ntlmrecon   # or persist it
```
