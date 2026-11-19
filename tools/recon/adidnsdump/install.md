---
tool: "adidnsdump"
build-system: "python"
upstream: "https://github.com/dirkjanm/adidnsdump"
recipe: "blackarch"
nix-package: "adidnsdump"
source: "https://github.com/dirkjanm/adidnsdump"
license: "custom:unknown"
blackarch-pkgver: "v1.6.0.r0.gc6bff9a"
---

# Installing adidnsdump

> Active Directory Integrated DNS dumping by any authenticated user.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adidnsdump) (version `v1.6.0.r0.gc6bff9a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S adidnsdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dirkjanm/adidnsdump) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `future`, `ldap3`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[adidnsdump]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#adidnsdump    # ad-hoc run, no install
nix profile install nixpkgs#adidnsdump   # or persist it
```
